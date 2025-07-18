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

One import:

```scala
import dev.cheleb.ziolaminartapir.*
```

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
PersonEndpoint.create( person ) // ??? 🧙
```

```scala {*|*|3}
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
```
````

<div v-motion style="position: absolute; width: 63%; border: 1px solid green; padding: 0px; margin: auto;"
  :initial="{ x: 300, y:-480 }"
  :enter="{ x: 1000, y:-400 }"
  :click-1="{ x: 400, y:-300 }"
  :leave="{ x: 50 }">

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

</div>


<div v-click="+4">

* and extension method to turn an endpoint into a function from Input to ZIO

````md magic-move {at:5}

```scala
extension [I, E <: Throwable, O](endpoint: PublicEndpoint[I, E, O, Any])
```

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

Ideally, we would like to process the result in the UI, with a minimal of boilerplate.

We have an endpoint definition, what would be nice would be to call it with a payload, and process the result in the UI.

Guess what, we can do that with ...  few extension methods.

-->


---

# Under the hood - Http Request
````md magic-move
```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
```
```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
              .runJs()                   // (2) Then run it fire and forget.
```
```scala
val person = Person("john.doe@foo.bar", "notsecured")
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( person )          // (1) RIO[SameOriginBackendClient, User]
              .emitTo(userBus, errorBus) // (2) Then run it, and process the result in UI.
```
````

<div v-click="+3">

We need antoher extension method to run the ZIO, and process the result in the UI.

````md magic-move {at:4}
```scala
extension [E <: Throwable, A](zio: RIO[SameOriginBackendClient, A])
```
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

No boilerplate, no magic, just Scala.

<div v-click="+1"><a href="https://cheleb.github.io/zio-laminar-tapir/docs/index.html">ZIO Laminar Tapir</a></div>
<ul>
  <li v-click="+2">Endpoint enhancement</li>
  <li v-click="+3">Marshalling/unmarshalling</li>
  <li v-click="+4">Error handling</li>
  <li v-click="+5">Reactive</li>
  <li v-click="+6">Type-safe</li>
  <li v-click="+7">Minimal boilerplate</li>
  <li v-click="+8">Different origin client</li>
  <li v-click="+9">Bearer token support (local storage)</li>

</ul>

<div v-click="+10" class="absolute left-30%">
```scala
PersonEndpoint //
          .create(personVar.now())
          .emitTo(userBus, errorBus)
```

</div>
