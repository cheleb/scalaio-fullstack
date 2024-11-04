# Tapir 101

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
        <li v-click><a href="localhost:8080/docs">OpenAPI documentation</a></li>
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

Given the following PersonService:

```scala
trait PersonService:
  def register(person: Person): Task[User]

```

<v-click>
We can implement the server side of the API like this:

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
