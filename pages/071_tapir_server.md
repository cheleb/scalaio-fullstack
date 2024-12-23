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
  :initial="{ x: 0, y:0 }"
  :enter="{ x: 350, y:-490 }"
  :click-1="{ x: 650, y:-490 }"
  :click-2="{ x: 60, y:-490 }"
  :leave="{ x: 300 }" />
 
<div v-click="+2">
  We are going to implement the API endpoints.
</div>

<!--

1. We will use Tapir to declare the API endpoints.
2. We will implement the server logic.
3. We will support the client side.

-->

---

# Controller: Tapir 101 by SoftwareMill

First step is to define the API endpoints as <span v-mark="{type:'circle', color:'orange', at:0, delay:2000}">values</span>.


````md magic-move

```scala
//                           In      Error     Out 
//                            ☟        ☟        ☟
val create: PublicEndpoint[Person, Throwable, User, Any] = ???
```

```scala {4|5|3,6|1,3,7-9}
//                            In 
//                            ☟
val create: PublicEndpoint[Person, Throwable, User, Any] = baseEndpoint
    .name("person")
    .post                 // POST method
    .in("person")         // Endpoint path is /person
    .in(              
      jsonBody[Person]    // Request body is JSON-encoded Person
    )
```

```scala {1,3,10}
//                                             Out 
//                                              ☟
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
//                                   Error
//                                     ☟
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
//                           In      Error     Out 
//                            ☟        ☟        ☟
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
<div v-click="+8">
  From this definition, Tapir will support:
    <ul>
        <li><a href="localhost:8080/docs/"><span v-mark="{type:'circle', color:'orange', at:[10,11]}">OpenAPI documentation</span></a></li>
        <li><span v-mark="{type:'underline', color:'orange', at:11}">Http server</span> squeleton</li>
        <li><span v-mark="{type:'underline', color:'orange', at:11}">Sttp client</span></li>
    </ul>
</div>
<div v-click="+9">
  POST /person HTTP/1.1
  
    {
        "email": "john.doe@foo.bar",
        "password": "notsecured",
        [ ... ]
    }
</div>
</div>


<!--

Tapir provides a type-safe way through a clean DSL to define API endpoints.

- `baseEndpoint` is a value that represents the root of the API, with common settings (error handling).

From API definition, Tapir will generate:
- OpenAPI documentation
- HTTP server squeleton
- Sttp client


-->



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
<img src="../images/youarehere.png" width="50"  v-motion duration="300ms"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 350, y:-490 }"
  :click-1="{ x: 650, y: -490 }"
  />

<div v-click="+2" v-motion style="position:absolute; border: 2px solid red; border-radius: 5px" 
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 750, y: -550 }"
  :leave="{ x: 50 }">
(1)
</div>  
<div v-click="+3" v-motion style="position:absolute; border: 2px solid red; border-radius: 5px" 
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 570, y: -480 }"
  :leave="{ x: 50 }">
(2)
</div>  

---

# Controller: Tapir / HTTP Server
<div v-click>Very classically, we will inject a service layer.</div>


````md magic-move
```scala
// Scala
trait UserService:
  def register(person: Person): Task[User]
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService)
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

     val create: ServerEndpoint[Any, Task] = ???
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):
                                             
  val create: ServerEndpoint[Any, Task] =  PersonEndpoint.create  // PublicEndpoint[Person, Throwable, User, Any]
```
```scala {5-9}
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create // PublicEndpoint[Person, Throwable, User, Any]
```
```scala
import sttp.tapir.ztapir.*

trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic { 
        // logic: Person => Task[User]
    }   
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic { (p: Person) => 
      userService.register(p) 
    }   
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic: (p: Person) =>
      userService.register(p)
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      userService.register
```
```scala
trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      userService.register
```
````

<div v-click=[6,8]  v-motion style="position:absolute; border: 2px solid green; border-radius: 5px"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 200, y:0 }"
  :leave="{ x: 50 }">

```scala
  //
  // Tapir sttp provides `zServerLogic` as an extension method on
  //
  extension [In, Out, R](e: Endpoint[In, Throwable, Out, R])
    def zServerLogic(logic: In => Task[Out]): ServerEndpoint[In, Task] = ???
```
</div>

<div v-click="+11" v-motion style="position:absolute"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 200, y:-150 }"
  :leave="{ x: 50 }">
  <img src="../images/red-square.svg" style="margin-left:1em; margin-top:10em; z-index:2"/>
  <img src="../images/backend-box.svg" style="width:100%; margin-top:-0.5em; z-index:1"/>
</div>

<div v-click="[9, 10]" v-motion style="position:absolute; border: 2px solid green; border-radius: 5px"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 300, y:0 }"
  :leave="{ x: 50 }">
  Who knows what is ETA expansion?
</div>


---

# Tapir / HTTP Server

```scala {*|4,8,15|19-20|1-2}{lines:true}
class UserController private (userService: UserService, jwtService: JWTService)
    extends SecuredBaseController[String, UserID](jwtService.verifyToken) {

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      UserService.register

  val login: ServerEndpoint[Any, Task] = PersonEndpoint.login.zServerLogic { lp =>
    for {
      user  <- UserService.login(lp.login, lp.password)
      token <- jwtService.createToken(user)
    } yield token
  }

  val profile: ServerEndpoint[Any, Task] = PersonEndpoint.profile.zServerAutenticatedLogic { userId => withPet =>
    UserService.getProfile(userId, withPet)
  }

  val routes: List[ServerEndpoint[Any, Task]] =
    List(create, login, profile)
}
```

<!--
We apply the same pattern for the other endpoints.
Note that extending `SecuredBaseController` will handle the JWT token verification (profile endoint)

-->

---

# Tapir / HTTP Server

<div grid="~ cols-2">
 <div>
   <img src="../images/lego.jpeg" />
  </div>
  <div>
   <v-clicks depth="2">

   - UserController
   - UserService
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

<!-- 

We need now to assemble the pieces.

-->


---

# Tapir / HTTP Server / ZLayer

````md magic-move {lines:true}
```scala
class UserController private (userService: UserService, jwtService: JWTService)    
```
```scala
class UserController private (userService: UserService, jwtService: JWTService)    
class UserServiceLive(
  userRepository: UserRepository,
  petRepository: PetRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends UserService
```
```scala
class UserController private (userService: UserService, jwtService: JWTService)    
class UserServiceLive( userRepository: UserRepository, petRepository: PetRepository, /* ... */ ) extends UserService
```
```scala
class UserController private (userService: UserService, jwtService: JWTService)    
class UserServiceLive( userRepository: UserRepository, petRepository: PetRepository, /* ... */ ) extends UserService
class JWTServiceLive( /* ... */ ) extends JWTService
```
````

<div v-click>
The wiring is done with ZLayer:


````md magic-move {lines:true, at:5}

```scala
object UserController:
```
```scala
object UserController:
  def makeZIO: URIO[UserService & JWTService, UserController] = ???
```
```scala
object UserController:
  def makeZIO: URIO[UserService & JWTService, UserController] =
    for
      jwtService    <- ZIO.service[JWTService]
      userService <- ZIO.service[UserService]
    yield new UserController(userService, jwtService)

```
```scala
private def makeControllers: : URIO[UserService & JWTService, List[UserController]] = for {
    userController         <- UserController.makeZIO
  } yield List(userController)
```
```scala
private def makeControllers = for {
    healthController       <- HealthController.makeZIO
    userController         <- UserController.makeZIO
    organisationController <- OrganisationController.makeZIO
  } yield List(healthController, userController, organisationController)
```
```scala
private def makeControllers : ZIO[UserService & JWTService & OrganisationService, Nothing, List[BaseController]] = for {
    healthController       <- HealthController.makeZIO
    userController         <- UserController.makeZIO
    organisationController <- OrganisationController.makeZIO
  } yield List(healthController, userController, organisationController)
```
```scala
private def makeControllers : URIO[UserService & JWTService & OrganisationService, List[BaseController]] = for {
    healthController       <- HealthController.makeZIO
    userController         <- UserController.makeZIO
    organisationController <- OrganisationController.makeZIO
  } yield List(healthController, userController, organisationController)
```
```scala
val program: ZIO[FlywayService & UserService & OrganisationService & JWTService & Server, Throwable, Unit] = ???
```
```scala
val program: RIO[FlywayService & UserService & OrganisationService & JWTService & Server, Unit] = ???
```

````
</div>


<!-- 

ZLayer is a way to wire the different services together.
ZLayer data type also composes !

-->

---

# ZLayer


````md magic-move {lines:true}

```scala
val program: RIO[FlywayService & UserService & OrganisationService & JWTService & Server, Unit] = ???

override def run =
    program
```
```scala {*|1,6,8-11|8,11}
val program: RIO[FlywayService & UserService & OrganisationService & JWTService & Server, Unit] = ???

override def run =
    program
      .provide(
        Server.default,
        // Service layers
        UserServiceLive.layer,
        OrganisationServiceLive.layer,
        FlywayServiceLive.configuredLayer,
        JWTServiceLive.configuredLayer,
        // Repository layers
        UserRepositoryLive.layer,
        OrganisationRepositoryLive.layer,
        Repository.dataLayer
        // ,ZLayer.Debug.mermaid
      )
```
````

<div v-click>
 `ZLayer.Debug.mermaid` will generate a diagram of the layers.

[Mermaid](https://mermaid-js.github.io/mermaid-live-editor/edit/#pako:eNp9kcFOwzAMhl-l8gmkrYLBmNQDBwQcUCUkNsQlF6vxuog2qdxkKJr27ji0qIA0br_tz7_t5ACV0wQF1IzdLrvbKFtenClYE--Jc01bDI1XcC75S8k_NvEDY6qaikqzp7xydmvqwKRLjMQDuhD0mWu0pkdvnH2hzvXGO45fPc1IZvP5bVZe_YF_ujeT57Vgk0-u0WP5y2cpwGtPfGJYshjAhcjlIG_GnhMzV1J-etv8f-_oNPknuUohzKAlbtFoeeGDslmmwO-oJQWFSI38rkDZo3Chk4PoQae9ofAcaAYYvFtHW33HA3NvUD6rHZLHT8ZQl94)
</div>


<!--

Mermaid is a diagramming and charting tool that produces good looking diagrams. ZIO provides a way to generate a diagram of the layers, and compile time.

-->

---

# ZLayer

From macro to code:

````md magic-move {lines:true}

```scala
class UserServiceLive private (
  userRepository: UserRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends UserService
```
```scala   
class UserServiceLive private (
  userRepository: UserRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends UserService

object UserServiceLive:

  val layer = ???
```
```scala   
class UserServiceLive private (
  userRepository: UserRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends UserService

object UserServiceLive:

  val layer = ZLayer.derive[UserServiceLive]
```

```scala
class UserServiceLive private (
  userRepository: UserRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends UserService

object UserServiceLive:
  // Expands to:
  val layer: RLayer[UserRepository & JWTService & Postgres[SnakeCase], UserService] = ZLayer.derive[UserServiceLive]

```
```scala
class UserServiceLive private (
  userRepository: UserRepository,
  jwtService: JWTService,
  quill: Quill.Postgres[SnakeCase]
) extends UserService

object UserServiceLive:
  // Expands to:
  val layer: RLayer[UserRepository & JWTService & Postgres[SnakeCase], UserService] = ZLayer{
    for
      userRepository  <- ZIO.service[UserRepository]
      petRepository   <- ZIO.service[PetRepository]
      jwtService      <- ZIO.service[JWTService]
      pgService       <- ZIO.service[Postgres[SnakeCase]]
    yield new UserServiceLive(userRepository, petRepository, jwtService, pgService)
  }

```
````

<!-- 

For simple cases, ZIO provides a way to generate a layer from a service implementation. Macro expansion is a good way to understand what is going on.



-->

---

# ZLayer: Manual Wiring

````md magic-move {lines:true}
```scala

object JWTServiceLive {
  val layer: URLayer[JWTConfig, JWTServiceLive] = ZLayer(
    for
      jwtConfig <- ZIO.service[JWTConfig]
      clock     <- Clock.javaClock
    yield JWTServiceLive(jwtConfig, clock)
  )
}
```
```scala

object JWTServiceLive {
  val layer: URLayer[JWTConfig, JWTServiceLive] = ZLayer(
    for
      jwtConfig <- ZIO.service[JWTConfig]
      clock     <- Clock.javaClock
    yield JWTServiceLive(jwtConfig, clock)
  )

  val jwtConfigLayer: TaskLayer[JWTConfig] = Configs.makeConfigLayer[JWTConfig]("jwt")

}
```
```scala

object JWTServiceLive {
  val layer: URLayer[JWTConfig, JWTServiceLive] = ZLayer(
    for
      jwtConfig <- ZIO.service[JWTConfig]
      clock     <- Clock.javaClock
    yield JWTServiceLive(jwtConfig, clock)
  )

  val jwtConfigLayer: TaskLayer[JWTConfig] = Configs.makeConfigLayer[JWTConfig]("jwt")

  val configuredLayer: URLayer[JWTConfig, JWTServiceLive] = JWTServiceLive.layer
}
```
```scala

object JWTServiceLive {
  val layer: URLayer[JWTConfig, JWTServiceLive] = ZLayer(
    for
      jwtConfig <- ZIO.service[JWTConfig]
      clock     <- Clock.javaClock
    yield JWTServiceLive(jwtConfig, clock)
  )

  val jwtConfigLayer: TaskLayer[JWTConfig] = Configs.makeConfigLayer[JWTConfig]("jwt")

  val configuredLayer: TaskLayer[JWTConfig] = jwtConfigLayer >>> JWTServiceLive.layer
}
```
```scala

object JWTServiceLive {
  val layer: URLayer[JWTConfig, JWTServiceLive] = ZLayer(
    for
      jwtConfig <- ZIO.service[JWTConfig]
      clock     <- Clock.javaClock
    yield JWTServiceLive(jwtConfig, clock)
  )

  val configuredLayer: URLayer[JWTServiceLive] =
    Configs.makeConfigLayer[JWTConfig]("jwt") >>> JWTServiceLive.layer
}
```
````

<!--

We can also wire the layers manually, generating a layer from a configuration for exmaple.

-->


---

# Tapir / Client - Server

<img v-click="+1" src="../images/chrome.svg" style="margin:20px; width:10%;" />

<img v-click="+2" src="../images/tapir-api-client.svg" style="width:30%;" />

<img src="../images/tapir-api-server.svg" style="width:30%;" />

