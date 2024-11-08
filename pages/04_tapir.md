# Tapir 101 by SoftwareMill

First step is to define the API endpoints as <span v-mark="{type:'circle', color:'orange', at:0, delay:2000}">values</span>.

<div v-click="+1">

````md magic-move {at:2}

```scala
//                                   In      Error     Out 
//                                    ☟        ☟        ☟
val createEndpoint: PublicEndpoint[Person, Throwable, User, Any] = ???
```

```scala {4|5|3,6|1,3,7-9}
//                                   In 
//                                    ☟
val createEndpoint: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
```

```scala {1,3,10}
//                                                    Out 
//                                                     ☟
val createEndpoint: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
    .out(jsonBody[User])  // Response is JSON-encoded User
```

```scala {3-6}
//                                   In      Error     Out 
//                                    ☟        ☟        ☟
val createEndpoint: PublicEndpoint[Person, Throwable, User, Any] = endpoint
    .errorOut(statusCode and plainBody[String])
    .mapErrorOut[Throwable](HttpError.decode)(HttpError.encode)
    .prependIn("api")
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
    .out(jsonBody[User])  // Response is JSON-encoded User
```

```scala
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

````
<div grid="~ cols-2 gap-4">
<div v-click="+10">
  From this definition, Tapir can generate:
    <ul>
        <li v-click><a href="localhost:8080/docs/">OpenAPI documentation</a></li>
        <li v-click><span v-mark="{type:'circle', color:'orange', at:10}">Http server</span> squeleton</li>
        <li v-click><span v-mark="{type:'circle', color:'orange', at:11}">Sttp client</span></li>
    </ul>
</div>
<div v-click="+12">
  POST /person HTTP/1.1
  
    {
        "email": "john.doe@foo.bar",
        "password": "notsecured",
        [ ... ]
    }
</div>
</div>

</div>

---

# Tapir / HTTP Server

After a bunch of imports, we can implement the server side of the API.

<div grid="~ cols-[20%_70%] gap-0">
<div v-click="+9" >
  <img src="../images/red-square.svg" style="margin-left:1em; margin-top:10em; z-index:2"/>
  <img src="../images/backend-box.svg" style="width:100%; margin-top:-0.5em; z-index:1"/>
</div>

<div>

Given:

```scala
trait PersonService:
  def register(person: Person): Task[User]

```

<v-click>
Controller implementation:


````md magic-move

```scala
class PersonController private (personService: PersonService, jwtService: JWTService)
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create // PublicEndpoint[Person, Throwable, User, Any]
     
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create // PublicEndpoint[Person, Throwable, User, Any]

  //
  // Tapir sttp provide `zServerLogic` as an extension method on
  extension [In, Out, R](e: Endpoint[In, Throwable, Out, R])
    def zServerLogic(logic: In => Task[Out]): ServerEndpoint[In, Task] = ???
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic { 
        // Logic here ..
    }   
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic { (p: Person) => 
      personService.register(p) 
    }   
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic: (p: Person) =>
      personService.register(p)
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic:
      personService.register
```
```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      personService.register
```
````

</v-click>
</div>
</div>

---

# Tapir / HTTP Server

```scala {*|5,9,16|20-21|1-3}{lines:true}
class PersonController private (personService: PersonService, jwtService: JWTService)
    extends BaseController
    with SecuredBaseController[String, UserID](jwtService.verifyToken) {

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      personService.register

  val login: ServerEndpoint[Any, Task] = PersonEndpoint.login.zServerLogic { lp =>
    for {
      user  <- personService.login(lp.login, lp.password)
      token <- jwtService.createToken(user)
    } yield token
  }

  val profile: ServerEndpoint[Any, Task] = PersonEndpoint.profile.securedServerLogic { userId => withPet =>
    personService.getProfile(userId, withPet)
  }

  val routes: List[ServerEndpoint[Any, Task]] =
    List(create, login, profile)
}
```

---

# Tapir / HTTP Server

````md magic-move {lines:true}

```scala {*|2,6}
object PersonController:
  def makeZIO: URIO[PersonService & JWTService, PersonController] =
    for
      jwtService    <- ZIO.service[JWTService]
      personService <- ZIO.service[PersonService]
    yield new PersonController(personService, jwtService)
```

```scala
object PersonController:
  def makeZIO: URIO[PersonService & JWTService, PersonController] = ???
```
```scala {*|4|5|6|7}
object PersonController:
  def makeZIO: URIO[PersonService & JWTService, PersonController] = ???

class PersonServiceLive private (
  userRepository: UserRepository,
  petRepository: PetRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends PersonService

```
```scala
object PersonController:
  def makeZIO: URIO[PersonService & JWTService, PersonController] = ???

class PersonServiceLive private ( ... ) extends PersonService
class JWTServiceLive private ( ... ) extends JWTService
```
```scala
object PersonController:
  def makeZIO: URIO[PersonService & JWTService, PersonController] = ???

class PersonServiceLive private ( ... ) extends PersonService
class JWTServiceLive private ( ... ) extends JWTService

object PersonServiceLive {
  val layer: RLayer[UserRepository & PetRepository & JWTService & Postgres[SnakeCase], PersonService] =
    ZLayer.derive[PersonServiceLive]
}
```
```scala {*|10|9|8-12}
object PersonController:
  def makeZIO: URIO[PersonService & JWTService, PersonController] = ???

class PersonServiceLive private ( ... ) extends PersonService
class JWTServiceLive private ( ... ) extends JWTService

object PersonServiceLive {
  val layer: RLayer[
      UserRepository & PetRepository & JWTService & Postgres[SnakeCase],
      PersonService
    ] =
    ZLayer.derive[PersonServiceLive]
}
```
````

---

# Tapir / HTTP Server

````md magic-move {lines:true}

```scala
private val program: RIO[FlywayService & PersonService & JWTService & Server, Unit] =
    for {
      _ <- runMigrations
      _ <- server
    } yield ()
```
```scala
private val program: RIO[FlywayService & PersonService & JWTService & Server, Unit] =
    for {
      _ <- runMigrations
      _ <- server
    } yield ()

override def run =
    program
      .provide(
        Server.default,
        // Service layers
        PersonServiceLive.layer,
        FlywayServiceLive.configuredLayer,
        JWTServiceLive.configuredLayer,
        // Repository layers
        UserRepositoryLive.layer,
        PetRepositoryLive.layer,
        Repository.dataLayer
      )
```
````

[Mermaid](https://mermaid-js.github.io/mermaid-live-editor/edit/#pako:eNp9kcFOwzAMQH8l8gmkrWIbcOiBA2IcUA6IDXHJxWrcLVqbVG4yVE37d9ytqL3AIZJlvzw7zgmKYAly2DE2e_W8NV7f3RjYEB-JM0slpioauJX8QvIf1ITWxcBdZjGixo5Yqmo-f1J6KcA7xZHR7khZNTC94cqthPtsif8G74V4-9r2U7iCLtUi-NLtEpPVE6Gc1VX6cGnObfDTWxPncuB6_RguxvBRDK9V943d_31hBjVxjc7K3k7GK2Ug7qkmA7mEFvlgwPizcKmRLdHa9o-EvMSqpRlgimHT-QLyyIl-oReH8gf1QJ1_AL7HiI4)


---

# Tapir / Client - Server

<img v-click="+1" src="../images/chrome.svg" style="margin:20px; width:10%;" />

<img v-click="+2" src="../images/tapir-api-client.svg" style="width:30%;" />

<img src="../images/tapir-api-server.svg" style="width:30%;" />


---

# Tapir / Sttp Client

In the same way, we can implement the client side of the API.

And we want to process the result in the UI.

<ul>
  <li v-click="+1">stay in the Scala world</li>
  <li v-click="+2">use a type-safe API</li>
  <li v-click="+3">use reactive stuff both http client and UI side</li>
  <li v-click="+4">with a minimal of boilerplate</li>
</ul>
