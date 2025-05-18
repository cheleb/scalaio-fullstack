# Tapir / Sttp Client

In the same way, we can implement the client side of the API.

And we want to process the result in the UI.

<ul>
  <li v-click="+1">stay in the Scala world</li>
  <li v-click="+2">use a type-safe API</li>
  <li v-click="+3">use reactive stuff both http client and UI side</li>
  <li v-click="+4">with a minimal of boilerplate</li>
</ul>


---


<img src="../images/architecture.svg" style="width: 100%" />
<img src="../images/youarehere.png" width="50" v-motion
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 700, y:-490 }"
  :click-1="{ x: 200, y:-490 }"
  :leave="{ x: 50 }"/>

<div v-click="+1">
.
</div>


---

# One line to run the request


````md magic-move
```scala {*|1-3|7|13-16}
val personVar = Var(
  NewUser("", "", "", Password(""), Password(""))
)
div(
  h1("Signup"),
  div(
    personVar.asForm
  ),
  div(
    Button(
      "Create",
      onClick --> { _ =>
        UserEndpoint
          .create(personVar.now())
          .emitTo(userBus, errorBus)
      
      }
    )
  )

```
````

---

# Tapir Client Side

With another bunch of imports, we can implement the client side of the API, in one liner:

Let say we have a Person instance:

````md magic-move

```scala
val person = Person("john.doe@foo.bar", "notsecured")
```

```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create
```

```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )   // 😳      
```

```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
```

```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
              .emitTo(userBus, errorBus) // (2) Then run it, and process the result in UI.
```
````


<ul>
  <li v-click="+3">First extention allows to consume the payload, and produce an RIO that depends on HttpClient</li>
  <li v-click="+4">`emitTo` is another extension method, we will detail shortly</li>
</ul>

<!--

Ideally, we would like to process the result in the UI, with a minimal of boilerplate.

We have an endpoint definition, what would be nice would be to call it with a payload, and process the result in the UI.

Guess what, we can do that with ...  few extension methods.

-->

---

# Tapir 101

```scala
//                                   In      Error     Out 
//                                    ☟        ☟        ☟
val create: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
    .out(jsonBody[User])  // Response is JSON-encoded User
```

<!--

The value is even not related to HttpClient, it's just an endpoint definition.

However, we can call it with a payload, and process the result in the UI.

Where is the magic?

-->


---

# Under the hood - Http Request

Inspired from [Rock The JVM](https://rockthejvm.com) courses/blog/videos ZIO rite of passage.

<div v-click="+1">
```scala
import dev.cheleb.ziolaminartapir.*
```
</div>
<div v-click="+2">
Et voilà, you can now use Tapir in a reactive way, in your ScalaJS project.
</div>

<div v-click="+3">
```scala
// This import brings bunch of extension to Endpoint and ZIO.
```
</div>

<div v-click="+4">

````md magic-move {at:5}

```scala
extension [I, E <: Throwable, O](endpoint: Endpoint[Unit, I, E, O, Any])
```

```scala {*|1|4|6}
extension [I, E <: Throwable, O](endpoint: Endpoint[Unit, I, E, O, Any])
  /** Call the endpoint with a payload, and get a ZIO back.
    */
  def apply(payload: I): RIO[SameOriginBackendClient, O] =
    ZIO
      .service[SameOriginBackendClient]
      .flatMap(_.endpointRequestZIO(endpoint)(payload))
```
````
</div>


<!--

In Scala, we an object has an "apply" method, we can call it without the method name, hence it become a function.
The function signature is `I => RIO[SameOriginBackendClient, O]`.

-->

---

# Under the hood - Http Request

```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
              .emitTo(userBus, errorBus) // (2) Then run it, and process the result in UI.
```

<div v-click="+1">

We need antoher extension method to run the ZIO, and process the result in the UI.

````md magic-move {at:2}
```scala
extension [E <: Throwable, A](zio: ZIO[SameOriginBackendClient, E, A])
```
```scala
extension [E <: Throwable, A](zio: ZIO[SameOriginBackendClient, E, A])

  def emitTo( bus: EventBus[A], error: EventBus[E]): Unit =
    zio
      .tapError(e => ZIO.attempt(error.emit(e)))
      .tap(a => ZIO.attempt(bus.emit(a)))
      .exec
```
```scala
extension [E <: Throwable, A](zio: ZIO[SameOriginBackendClient, E, A])

  def emitTo( bus: EventBus[A], error: EventBus[E]): Unit =
    zio
      .tapError(e => ZIO.attempt(error.emit(e)))
      .tap(a => ZIO.attempt(bus.emit(a)))
      .exec

  private def exec: Unit =
    Unsafe.unsafe { implicit unsafe =>
      Runtime.default.unsafe.fork(
        zio.provide(SameOriginBackendClientLive.configuredLayer)
      )
    }

```
````

</div>



---

# Under the hood - Http Request

There are a few more extension methods:

* Different origin client
* Bearer token support (local storage)

<div v-click="+1">

```scala
PersonEndpoint.profile(false).emitTo(userBus)
```

For the curious, the `profile` endpoint is defined as:

```scala
val profile: Endpoint[String, Boolean, Throwable, (User, Option[Pet]), Any]
```

</div>

---

# Under the hood - Http Request

```scala
val profile: Endpoint[String, Boolean, Throwable, (User, Option[Pet]), Any]
```

Yet another extension method, to handle secured endpoint.

```scala {*|1|6|8}{lines:true}
extension [I, E <: Throwable, O](endpoint: Endpoint[String, I, E, O, Any])

  // Call the secured endpoint with a payload, and get a ZIO back.

  @targetName("securedApply")
  def apply[UserToken <: WithToken](
      payload: I
  )(using session: Session[UserToken]): RIO[SameOriginBackendClient, O] =

    ZIO
      .service[SameOriginBackendClient]
      .flatMap(_.securedEndpointRequestZIO(endpoint)(payload))

```

---

# Under the hood - Http Request

No boilerplate, no magic, just Scala.

<div v-click="+1"><a href="https://cheleb.github.io/zio-laminar-tapir/docs/index.html">ZIO Laminar Tapir</a></div>
<ul>
  <li v-click="+2">Endpoint enhancement</li>
  <li v-click="+3">Marshalling/unmarshalling</li>
  <li v-click="+4">Error handling</li>
  <li v-click="+5">Reactive</li>
  <li v-click="+6">Type-safe</li>
  <li v-click="+7">Minimal boilerplate</li>
</ul>

<div v-click="+8" class="absolute left-30%">
```scala
PersonEndpoint //
          .create(personVar.now())
          .emitTo(userBus, errorBus)
```

</div>



---

# Tapir Sttp

Both server and client side are implemented with Tapir and Sttp.

* Tapir: API definition
* Sttp as HTTP client: One line to run the request
* Sttp as backend ZIO: One line handle the request

In the mean time:
* Json marshalling/unmarshalling
* Error handling
* Security (Bearer token)
* Reactive