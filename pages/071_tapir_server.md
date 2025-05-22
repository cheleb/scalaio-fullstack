
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
     //          ServerEndpoint[-R,  F[_]] 
                                      PersonEndpoint.create  // PublicEndpoint[Person, Throwable, User, Any]

```
```scala
import sttp.tapir.ztapir.*

trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):
     //          ServerEndpoint[-R,  F[_]]                                              
                                      PersonEndpoint.create  // PublicEndpoint[Person, Throwable, User, Any]
```
```scala {5-9}
import sttp.tapir.ztapir.*

trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):
     //          ServerEndpoint[-R,  F[_]]                                              
                                      PersonEndpoint.create  // PublicEndpoint[Person, Throwable, User, Any]
```
```scala
import sttp.tapir.ztapir.*

trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

                                      PersonEndpoint.create
                                             .zServerLogic { 
                                             // logic: Person => Task[User]
                                             }   
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
import sttp.tapir.ztapir.*

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
import sttp.tapir.ztapir.*

trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic: (p: Person) =>
      userService.register(p)
```
```scala
import sttp.tapir.ztapir.*

trait UserService:
  def register(person: Person): Task[User]
// ...
class UserController private (userService: UserService, jwtService: JWTService):

  val create: ServerEndpoint[Any, Task] = PersonEndpoint.create
    .zServerLogic:
      userService.register
```
```scala
import sttp.tapir.ztapir.*

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

<div v-click="+12" v-motion style="position:absolute"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 200, y:-150 }"
  :leave="{ x: 50 }">
  <img src="../images/red-square.svg" style="margin-left:1em; margin-top:10em; z-index:2"/>
  <img src="../images/backend-box.svg" style="width:100%; margin-top:-0.5em; z-index:1"/>
</div>

<div v-click="[10, 11]" v-motion style="position:absolute; border: 2px solid green; border-radius: 5px"
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
  
  def make: UserController =
      new UserController(userService, jwtService)

```

```scala
object UserController:

  def makeZIO: UIO[UserController] =
    for
       // ...
    yield new UserController(userService, jwtService)

```
```scala
object UserController:

  def makeZIO: URIO[JWTService, UserController] =
    for
      jwtService    <- ZIO.service[JWTService]
    yield new UserController(userService, jwtService)

```
```scala
object UserController:

  def makeZIO: URIO[UserService & JWTService, UserController] =
    for
      jwtService    <- ZIO.service[JWTService]
      userService   <- ZIO.service[UserService]
    yield new UserController(userService, jwtService)

```
```scala
private def makeControllers : URIO[UserService & JWTService & OrganisationService, List[BaseController]] = for {
    healthController       <- HealthController.makeZIO
    userController         <- UserController.makeZIO
    organisationController <- OrganisationController.makeZIO
  } yield List(healthController, userController, organisationController)
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

override def run: Task[Unit] =
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

[Mermaid](https://mermaid.live/edit/#pako:eNqFkV1rgzAUhv-KnKsNrFSNNeZiF_u6GI7BujEYuQk1VVlNJGo3V_rfd_yi3Vi3gHCOed73TXJ2sNKJBAapEWVmXT5xFc_POCyl2UpzpdU6T52NaKXhcI57Lu7dbtp30XZEvpJxvpXOqucaI5P4gHqIPphUqLwSda7Voyx1ldfatL1mMrVmswsr9n_Ax-5H8QSxg4-TiFrE33wCBJ4raU6EdRYD6GEZDOVi1JzKHCksQyTvXp7-vfoooYjfyyr74zD40QGORvi3U4CN88kTYLVppA2FNIXoWthxZVkc6kwWkgPDMhHmjQNXe9SUQr1qXUwyo5s0A7YWmwq7psTXk9e5wMkfEKmSbuyNqoF5YdB7ANvBBzA_dDw_dAn1KIlcz3VtaIG5hDgLGhKfRgtCiR8Gexs--9S5g_8jXGROw8ALiD-l3iTda0yhoqn1slWrod9_AQtf1uQ)
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

  val configuredLayer: URLayer[JWTConfig, JWTServiceLive] =
                                                            JWTServiceLive.layer
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

  val configuredLayer: URLayer[JWTConfig, JWTServiceLive] =
                                             jwtConfigLayer >>> JWTServiceLive.layer
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

  val configuredLayer: TaskLayer[JWTServiceLive] = 
                                             jwtConfigLayer >>> JWTServiceLive.layer
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

