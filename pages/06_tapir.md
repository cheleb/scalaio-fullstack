# Agenda

* One line to setup a new project ✅
* One line to start the dev environment ✅
* One line to bind data in the UI ✅
* One line to expose API 👈
* One line to consume API
* One line to deploy

---

<img src="../images/architecture.svg" style="width: 100%" />
<img src="../images/youarehere.png" width="50"  v-motion
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 350, y:-490 }"
  :leave="{ x: 50 }"/>


---

# Controller: Tapir 101 by SoftwareMill

First step is to define the API endpoints as <span v-mark="{type:'circle', color:'orange', at:0, delay:2000}">values</span>.


````md magic-move {at:2}

```scala
//                                   In      Error     Out 
//                                    ☟        ☟        ☟
val create: PublicEndpoint[Person, Throwable, User, Any] = ???
```

```scala {4|5|3,6|1,3,7-9}
//                                   In 
//                                    ☟
val create: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
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
val create: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
    .out(jsonBody[User])  // Response is JSON-encoded User
```

```scala {1,3-6}
//                                           Error
//                                             ☟
val create: PublicEndpoint[Person, Throwable, User, Any] = endpoint
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
val create: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
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
<div v-click="+9">
  From this definition, Tapir will support:
    <ul>
        <li><a href="localhost:8080/docs/"><span v-mark="{type:'circle', color:'orange', at:[11,12]}">OpenAPI documentation</span></a></li>
        <li><span v-mark="{type:'underline', color:'orange', at:12}">Http server</span> squeleton</li>
        <li><span v-mark="{type:'underline', color:'orange', at:12}">Sttp client</span></li>
    </ul>
</div>
<div v-click="+10">
  POST /person HTTP/1.1
  
    {
        "email": "john.doe@foo.bar",
        "password": "notsecured",
        [ ... ]
    }
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

<img src="../images/architecture.svg" style="width: 100%" />
<img src="../images/youarehere.png" width="50"  v-motion
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 350, y:-490 }"
  :click-1="{ x: 650, y: -490 }"
  />

<div v-click="+1" v-motion style="position:absolute; border: 2px solid red; border-radius: 5px" 
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 750, y: -550 }"
  :leave="{ x: 50 }">
(1)
</div>  
<div v-click v-motion style="position:absolute; border: 2px solid red; border-radius: 5px" 
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 570, y: -480 }"
  :leave="{ x: 50 }">
(2)
</div>  

---

# Controller: Tapir / HTTP Server
<div v-click>Very classically, we will inject a service layer.</div>

<div grid="~ cols-[20%_70%] gap-0">
<div v-click="+11" >
  <img src="../images/red-square.svg" style="margin-left:1em; margin-top:10em; z-index:2"/>
  <img src="../images/backend-box.svg" style="width:100%; margin-top:-0.5em; z-index:1"/>
</div>

<div>

````md magic-move
```scala
// Scala
trait PersonService:
  def register(person: Person): Task[User]
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService)
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create // PublicEndpoint[Person, Throwable, User, Any]  
```
```scala {5-9}
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create // PublicEndpoint[Person, Throwable, User, Any]
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic { 
        // Logic here ..
    }   
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic { (p: Person) => 
      personService.register(p) 
    }   
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic: (p: Person) =>
      personService.register(p)
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
    .zServerLogic:
      personService.register
```
```scala
trait PersonService:
  def register(person: Person): Task[User]
// ...
class PersonController private (personService: PersonService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      personService.register
```
````
</div>
</div>

<div v-click=[5,7]  v-motion style="position:absolute; border: 2px solid green; border-radius: 5px"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 200, y:-100 }"
  :leave="{ x: 50 }">

```scala
  //
  // Tapir sttp provides `zServerLogic` as an extension method on
  //
  extension [In, Out, R](e: Endpoint[In, Throwable, Out, R])
    def zServerLogic(logic: In => Task[Out]): ServerEndpoint[In, Task] = ???
```
</div>

---

# Tapir / HTTP Server

```scala {*|4,8,15|19-20|1-2}{lines:true}
class PersonController private (personService: PersonService, jwtService: JWTService)
    extends SecuredBaseController[String, UserID](jwtService.verifyToken) {

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
