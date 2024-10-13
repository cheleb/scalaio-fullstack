# Tapir

Tapir is a Softwaremil project that allow to describe a REST API in Scala as value.

```scala {*|5|6|8|10}
//                                   In      Error     Out 
//                                    ☟        ☟        ☟
val createEndpoint: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
    .out(jsonBody[User])  // Response is JSON-encoded User
```
<div grid="~ cols-2 gap-4">
<div v-click="+5">
  From this definition, Tapir can generate:
    <ul>
        <li v-click="+6"><a href="localhost:8080/docs">OpenAPI documentation</a></li>
        <li v-click="+7"><span v-mark="{type:'circle', color:'orange', at:10}">Http server</span> squeleton</li>
        <li v-click="+8"><span v-mark="{type:'circle', color:'orange', at:11}">Sttp client</span></li>
    </ul>
</div>
<div v-click="+9">
  POST /person HTTP/1.2
  
    {
        "email": "john.doe@foo.bar",
        "password": "notsecured",
        [ ... ]
    }
</div>
</div>

---

# ZIO 101

ZIO is a library for asynchronous and concurrent programming in Scala. 

Simplified, ZIO is a data type that represents a computation:
* that may fail with an error of type `E`
* that may succeed with a value of type `A`
* that may require an environment of type `R`

```scala
type ZIO[R,E,A] = R => Either[E, A]
```

Many aliases are provided for common use cases:

```scala 
type IO[+E, +A]   = ZIO[Any, E, A]         // Succeed with an `A`, may fail with `E`        , no requirements.
type Task[+A]     = ZIO[Any, Throwable, A] // Succeed with an `A`, may fail with `Throwable`, no requirements.
type RIO[-R, +A]  = ZIO[R, Throwable, A]   // Succeed with an `A`, may fail with `Throwable`, requires an `R`.
type UIO[+A]      = ZIO[Any, Nothing, A]   // Succeed with an `A`, cannot fail              , no requirements.
type URIO[-R, +A] = ZIO[R, Nothing, A]     // Succeed with an `A`, cannot fail              , requires an `R`.
```

---

# Tapir Server Side

After a bunch of imports, we can implement the server side of the API.

Given the following PersonServide:

```scala
trait PersonService:
  def register(person: Person): Task[User]

```

<v-click>
We can implement the server side of the API like this:

````md magic-move

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic { 
        // Logic here ..
    }   
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic { (p: Person) => 
      personService.register(p) 
    }   
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic: (p: Person) =>
      personService.register(p)
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      personService.register
```
````

</v-click>

<div v-click="[1,2]" style="border: solid 1px; "
  v-motion
  :enter="{ x: 250, y: -60 }"
  :delay="1000"
  :duration="600"
>
```scala
def zServerLogic[R](logic: Person => RIO[R, User]): ZServerEndpoint[R, Any]
```
</div>


---

# Tapir Client Side

With another bunch of imports, we can implement the client side of the API, in one liner:

Let say we have a Person instance:

```scala
val john = Person("john.doe@foo.bar", "notsecured")
```

````md magic-move
```scala
// From our endpoint definition
PersonEndpoint.create
```

```scala
// Just call the endpoint with the instance
PersonEndpoint.create(john) // RIO[SameOriginBackendClient, User]
```

```scala
// Then run it, and process the result in UI.
PersonEndpoint
              .create(personVar.now())
              .emitTo(userBus, errorBus)
```
````


<div v-click="[2]"
  v-motion
  :delay="2000"
  :duration="600"
>
     Et voilà! <a href="localhost:5173/">Demo time!</a>


</div>

