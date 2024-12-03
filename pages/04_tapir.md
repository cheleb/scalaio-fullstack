# Controller: Tapir 101 by SoftwareMill

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
  From this definition, Tapir will generate:
    <ul>
        <li v-click><a href="localhost:8080/docs/"><span v-mark="{type:'circle', color:'orange', at:10}">OpenAPI documentation</span></a></li>
        <li v-click><span v-mark="{type:'circle', color:'orange', at:11}">Http server</span> squeleton</li>
        <li v-click><span v-mark="{type:'circle', color:'orange', at:12}">Sttp client</span></li>
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

------

# Tapir

Tapir stands between Http transport and effect or direct style.
<div grid="~ cols-3 gap-4">
 <div>
  <h3>Http server</h3>
  <hr />
  <ul>
  <li v-click="+1" delay="1000"><span v-mark="{type:'circle', color:'orange', at:3}">ZIO-HTTP</span></li>
  <li v-click="+1" delay="2000">HTTP4S</li>
  <li v-click="+1" delay="3000">Netty</li>
  <li v-click="+1" delay="4000">Play</li>
  <li v-click="+1" delay="5000">...</li>
  </ul>
 </div>
 <div>
   <img v-click="+2" src="../images/tapir-zio.png" style="width: 100%;" />
 </div>
 <div>
  <h3>Effect or direct style</h3>
  <hr />
  <ul>
  <li v-click="+1" delay="6000"><span v-mark="{type:'circle', color:'orange', at:3}">ZIO</span></li>
  <li v-click="+1" delay="7000">Cat Effects</li>
  <li v-click="+1" delay="8000">Future</li>
  <li v-click="+1" delay="8000">Direct style</li>
  <li v-click="+1" delay="9000">...</li>
  </ul>

 </div>
</div>

---

# Controller: Tapir / HTTP Server

<div v-click>Controller implements server logic for the API endpoints.</div>
<div v-click>

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

---

# Controller: Tapir / HTTP Server
```scala
val create: PublicEndpoint[Person, Throwable, User, Any] = ???
```
<div v-click>Very classically, we will inject a service layer.</div>

<div grid="~ cols-[20%_70%] gap-0">
<div v-click="+11" >
  <img src="../images/red-square.svg" style="margin-left:1em; margin-top:10em; z-index:2"/>
  <img src="../images/backend-box.svg" style="width:100%; margin-top:-0.5em; z-index:1"/>
</div>

<div v-click="+1">

Given:

````md magic-move
```scala
// Scala
trait PersonService:
  def register(person: Person): Task[User]

```
```java
// Java
interface PersonService {
  Task<Person> register(Person: person)
}

```
```scala
// Scala
trait PersonService:
  def register(person: Person): ZIO[Any, Throwable, User]

```
```scala
// Scala
trait PersonService:
  def register(person: Person): Task[User]

```
````

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

```scala {5-9}
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create // PublicEndpoint[Person, Throwable, User, Any]

  //
  // Tapir sttp provides `zServerLogic` as an extension method on
  //
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

<div grid="~ cols-2">
 <div>
   <img src="../images/lego.jpeg" />
  </div>
  <div>
   <v-clicks depth="2">

   - PersonController
   - PersonService
   - JWTService

   </v-clicks>
  </div>
  <div v-click>
    We want to assemble the pieces.
  </div>
  <div>
  <v-clicks depth="2">

   - All with Effect System.
   - Fully reactive way (blocking or not).
   - At compile time.
   - With minimal boilerplate.
   </v-clicks>
  </div>
  
</div>


---

# Tapir / HTTP Server / ZIO

````md magic-move {lines:true}

```scala
val personService: PersonService = ???
```
```scala
val personService: PersonService = ???
val jwtService: JWTService = ???
```
```scala
val personService: PersonService = ???
val jwtService: JWTService = ???

val personController = new PersonController(personService, jwtService)
```

````

<div v-click>
The equivalent in ZIO:


````md magic-move {lines:true, at:4}

```scala
URIO[PersonService & JWTService, PersonController] // A ZIO effect ZIO[-R, +E, +A]
                                                   // to produce a PersonController
```


```scala {*|2|3}
URIO[
  PersonService & JWTService, // Requires PersonService and JWTService
  PersonController            // Produces a PersonController
]
```

```scala {*|2|2,6|4,5}
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

```
````
</div>

---

# ZLayer

ZLayer is a another ZIO DataType that can be used to compose services.


````md magic-move {lines:true}

```scala
object PersonServiceLive:

  val layer: RLayer[UserRepository & PetRepository & JWTService & Postgres[SnakeCase], PersonService] = ???

```
```scala {*|3|4}
object PersonServiceLive:
  val layer: RLayer[
    UserRepository & PetRepository & JWTService & Postgres[SnakeCase],
    PersonService] = ???

```
```scala
object PersonServiceLive:

  val layer: RLayer[UserRepository & PetRepository & JWTService & Postgres[SnakeCase], PersonService] =
    ZLayer.derive[PersonServiceLive]

```

```scala
object PersonServiceLive:
  // Expands to:
  val layer: RLayer[UserRepository & PetRepository & JWTService & Postgres[SnakeCase], PersonService] = ZLayer{
    for
      userRepository  <- ZIO.service[UserRepository]
      petRepository   <- ZIO.service[PetRepository]
      jwtService      <- ZIO.service[JWTService]
      pgService       <- ZIO.service[Postgres[SnakeCase]]
    yield new PersonServiceLive(userRepository, petRepository, jwtService, pgService)
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

[Mermaid](https://mermaid-js.github.io/mermaid-live-editor/edit/#pako:eNp9kcFOwzAMhl-l8gmkrYLBmNQDBwQcUCUkNsQlF6vxuog2qdxkKJr27ji0qIA0br_tz7_t5ACV0wQF1IzdLrvbKFtenClYE--Jc01bDI1XcC75S8k_NvEDY6qaikqzp7xydmvqwKRLjMQDuhD0mWu0pkdvnH2hzvXGO45fPc1IZvP5bVZe_YF_ujeT57Vgk0-u0WP5y2cpwGtPfGJYshjAhcjlIG_GnhMzV1J-etv8f-_oNPknuUohzKAlbtFoeeGDslmmwO-oJQWFSI38rkDZo3Chk4PoQae9ofAcaAYYvFtHW33HA3NvUD6rHZLHT8ZQl94)


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
