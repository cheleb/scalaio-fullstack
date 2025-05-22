
# Programming with values

Values are the core of functional programming. 

They are immutable and can be composed to create more complex values or programs.


````md magic-move
```

- Algebra (ADTs)
- Interpreter
```
```

- Algebra (ADTs)
  - ZIO algebra, effect definitions
  - Tapir algebra, HTTP definitions
- Interpreter
```
```

- Algebra (ADTs)
  - ZIO algebra, effect definitions
  - Tapir algebra, HTTP definitions
- Interpreter
  - ZIO interpreter
  - Tapir interpreter
```
```

- Algebra (ADTs)
  - ZIO algebra, effect definitions
  - Tapir algebra, HTTP definitions
- Interpreter
  - ZIO interpreter
    - ZIO runtime
    - ZIO test
  - Tapir interpreter
```
```

- Algebra (ADTs)
  - ZIO algebra, effect definitions
  - Tapir algebra, HTTP definitions
- Interpreter
  - ZIO interpreter
  - Tapir interpreter
    - Server interpreter
    - Client interpreter
    - Test interpreter
    - OpenAPI interpreter

```
````


---


# Tapir Algebra




<img src="../images/architecture-global.drawio.svg" style="width: 70%" />
<img src="../images/youarehere.png" width="50"  v-motion 
  :initial="{ x: 370, y:-440 }"
  :click-1="{ x: 370, y:-380 }"
   />
 
<div v-click="+2">
  We are going to implement the API endpoints.
</div>

<!--

1. We will use Tapir to declare the API endpoints.
2. We will implement the server logic.
3. We will support the client side.

-->

---

<img src="../images/architecture.svg" style="width: 70%" />
<img src="../images/youarehere.png" width="50"  v-motion duration="300ms"
  :initial="{ x: 50, y:-180 }"
  :enter="{ x: 230, y:-330 }"
  :click-1="{ x: 650, y: -490 }"
  />



---

# Tapir 101 Endpoints definitions

First step is to define the API endpoints as <span v-mark="{type:'circle', color:'orange', at:0, delay:2000}">values</span>.


````md magic-move

```scala
//                           In      Error     Out 
//                            ☟        ☟        ☟
val register: PublicEndpoint[Person, Throwable, User, Any] = ???
```

```scala {*|5|6|1,3,7-9}
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


