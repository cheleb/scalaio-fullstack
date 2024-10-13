---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Scala Full Stack
info: |
  ## Scala Full Stack

  Learn more at [ZIO <3 ScalaJS <3 Laminar](https://github.com/cheleb/zio-scalajs-laminar.g8)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# take snapshot for each slide in the overview
overviewSnapshots: true
---

# Scala Full Stack

With ZIO, Tapir && Laminar


<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
---

# Agenda


````md magic-move

```scala {*|1|3-9|11-15}

object ScalaFullStack:
  
  object SetUp:

    val scalfold = "sbt new cheleb/zio-scalajs-laminar.g8"

    val build = ("sbt", "npm/Vite", "Docker")

    val ide = "VSCode / Metals 🤘🏼"

  object Projects:

     object Frontend

     object Backend 


```

````

---

# Setup


```bash
sbt new cheleb/zio-scalajs-laminar.g8 --name=scalaio-full-stack

code scalaio-full-stack
```

* VSCode / Metals 🤘🏼
* npm / vite
* sbt
* Docker

---

## VSCode / Metals 🤘🏼

Task automation with `.vscode/tasks.json` and `launch.json`

````md magic-move {lines: true}
```json {7|9|11-12}
{
  "version": "2.0.0",
  "tasks": [
    {
       "label": "demo",
        "runOptions": {
            "runOn": "folderOpen"
        },
        "dependsOrder": "sequence",
        "dependsOn": [
            "setup",
            "runDemo"
        ],
        "problemMatcher": [],
        "group": {
            "kind": "build"
        }
    }
  ]
}
```

```json
{
    "label": "setup",
    "type": "shell",
    "command": "./scripts/setup.sh",
    "presentation": {
        "panel": "dedicated",
        "group": "runDevCmd",
        "close": true
    },
    "group": "build"
}
```

```json
{
    "label": "runDemo",
    "dependsOrder": "parallel",
    "dependsOn": [
        "docker",
        "serverRun",
        "fastLink",
        "npmDev"
    ],
    "problemMatcher": [],
    "group": {
        "kind": "build"
    }
},
```

````
---

## npm / vite

```js {5|8|12}
import { defineConfig } from "vite";
import scalaJSPlugin from "@scala-js/vite-plugin-scalajs";

export default defineConfig({
    plugins: [scalaJSPlugin({
        // path to the directory containing the sbt build
        // default: '.'
        cwd: '../..',

        // sbt project ID from within the sbt build to get fast/fullLinkJS from
        // default: the root project of the sbt build
        projectID: 'client',

        // URI prefix of imports that this plugin catches (without the trailing ':')
        // default: 'scalajs' (so the plugin recognizes URIs starting with 'scalajs:')
        uriPrefix: 'scalajs',
    })],
    build: {
        sourcemap: true,
    }
});
```

---

## sbt

```scala {*|1-2|3-5|6-8|9-10|11-12|13-14}
// Cross project support, to spread project resources between js and jvm world
addSbtPlugin("org.portable-scala" % "sbt-scalajs-crossproject" % "1.3.2")
// Scala.js support
addSbtPlugin("org.scala-js" % "sbt-scalajs"        % "1.17.0")
addSbtPlugin("org.scala-js" % "sbt-jsdependencies" % "1.0.2")
// Scala.js bundler
addSbtPlugin("ch.epfl.scala" % "sbt-scalajs-bundler"     % "0.21.1")
addSbtPlugin("ch.epfl.scala" % "sbt-web-scalajs-bundler" % "0.21.1")
// TypeScript support
addSbtPlugin("org.scalablytyped.converter" % "sbt-converter" % "1.0.0-beta44")
// Static file generator
addSbtPlugin("org.playframework.twirl" % "sbt-twirl" % "2.0.5")
// Will reStart server on code modification.
addSbtPlugin("io.spray" % "sbt-revolver" % "0.10.0")
```

---

# SBT Cross Project

* plugins.sbt
```scala
addSbtPlugin("org.portable-scala" % "sbt-scalajs-crossproject" % "1.3.2")
```

* build.sbt
```scala {1|2|8-9}
lazy val shared: CrossProject = crossProject(JSPlatform, JVMPlatform)
  .crossType(CrossType.Pure)
  .disablePlugins(RevolverPlugin)
  .in(file("modules/shared"))
  /* [...] */
  .settings(publish / skip := true)
  
lazy val sharedJvm: Project = shared.jvm
lazy val sharedJs: Project  = shared.js
```

---

# SBT Cross Project
<h4>Shared</h4>

```scala
lazy val shared: CrossProject = crossProject(JSPlatform, JVMPlatform)
// [...]  
lazy val sharedJvm: Project = shared.jvm
lazy val sharedJs: Project  = shared.js
```

<div grid="~ cols-2 gap-4">
<div>
<v-click>
<h4>Server</h4>
```scala {*|3}
lazy val server = project
  .settings( /* [...] */ )
  .dependsOn(sharedJvm)
```
</v-click>
</div>
<div>
<v-click>
<h4>Client</h4>
```scala {*|4}
lazy val client = project
  .enablePlugins(ScalaJSPlugin)
  .settings( /* [...] */ )
  .dependsOn(sharedJs)
```
</v-click>
</div>
</div>


---

````md magic-move {lines: true}


```scala
object ScalaFullStack:
  
  object Frontend

  object Backend  
```

```scala {3-6}
object ScalaFullStack:
  
  object Frontend:
    val laminar = "Type-safe, reactive UI library"
    val zio = "Type-safe, composable asynchronous and  concurrent programming"
    val tapir = "Type-safe HTTP client generator"

  object Backend:
```

```scala {8-11}
object ScalaFullStack:
  
  object Frontend:
    val laminar = "Type-safe, reactive UI library"
    val zio = "Type-safe, composable asynchronous and  concurrent programming"
    val tapir = "Type-safe HTTP client generator"

  object Backend:
    val tapir = "Type-safe HTTP client generator"
    val zio = "Type-safe, composable asynchronous and concurrent programming"
    val quill = "Compile-time query generation"  
```

```scala
object ScalaFullStack:
  
  object Frontend:
    val laminar = "Type-safe, reactive UI library"
  
  object Shared:
    val zio = "Type-safe, composable asynchronous and  concurrent programming"
    val tapir = "Type-safe HTTP client generator"

  object Server:
    val quill = "Compile-time query generation"

```
````

---

# Shared

What can be shared between the client and the server?

<ul>
 <li v-click="+1">Payloads</li>
 <li v-click="+2">Business logic</li>
 <li v-click="+3">Validation</li>
 <li v-click="+4">Error handling</li>
</ul>

<div v-click="+5" class="absolute left-30%"> And ... </div>
<div v-click="+6" class="absolute right-30%"> REST API definition </div>
 
------

# Tapir

Tapir stand between Http server and effect or direct style.

<div v-click="+1">
<img src="./images/tapir-zio.png" style="width: 100%;" />
</div>


---

# ZIO 101

ZIO is a library for asynchronous and concurrent programming in Scala. 

<div v-click="+1">
Simplified, ZIO is a data type that represents a computation:
```scala
trait ZIO[-R, +E, +A]
```
<div v-click="+2">

<ul>
  <li v-click="+2">that may require an environment of type `R`</li>
  <li v-click="+3">that may fail with an error of type `E`</li>
  <li v-click="+4">that may succeed with a value of type `A`
</li>
</ul>
</div>
</div>

---

# ZIO 101


A simple mental model is to think of ZIO as a function:
```scala
type ZIO[R,E,A] = R => Either[E, A]
```
<div v-click="+1">
Many aliases are provided for common use cases:

```scala {*|*|2-3}
type IO[+E, +A]   = ZIO[Any, E, A]         // Succeed with an `A`, may fail with `E`        , no requirements.
type Task[+A]     = ZIO[Any, Throwable, A] // Succeed with an `A`, may fail with `Throwable`, no requirements.
type RIO[-R, +A]  = ZIO[R, Throwable, A]   // Succeed with an `A`, may fail with `Throwable`, requires an `R`.
type UIO[+A]      = ZIO[Any, Nothing, A]   // Succeed with an `A`, cannot fail              , no requirements.
type URIO[-R, +A] = ZIO[R, Nothing, A]     // Succeed with an `A`, cannot fail              , requires an `R`.
```
</div>


---

# Tapir 101

First step is to define the API endpoints as <span v-mark="{type:'circle', color:'orange', at:1}">values</span>.


```scala {*|*|5|6|8|10} {at:2}
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
<div v-click="+6">
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
class PersonController private (personService: PersonService, jwtService: JWTService)
```

```scala
class PersonController private (personService: PersonService, jwtService: JWTService):

  PersonEndpoint.create
     
```

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


<div v-click="+7" class="absolute left-10%">
`zServerLogic` is a Tapir extension method.
</div>



---

# Tapir Client Side

With another bunch of imports, we can implement the client side of the API, in one liner:

Let say we have a Person instance hold in a Var:

````md magic-move
```scala
val personVar = Var(Person("john.doe@foo.bar", "notsecured"))
```

```scala
val personVar = Var(Person("john.doe@foo.bar", "notsecured"))
// From our endpoint definition that Person ~~> User
PersonEndpoint.create
```

```scala
val personVar = Var(Person("john.doe@foo.bar", "notsecured"))
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( personVar.now() ) // RIO[SameOriginBackendClient, User]
```

```scala
val personVar = Var(Person("john.doe@foo.bar", "notsecured"))
// From our endpoint definition that Person ~~> User
PersonEndpoint.create( personVar.now() ) // RIO[SameOriginBackendClient, User]
              .emitTo(userBus, errorBus) // Then run it, and process the result in UI.
```
````


<ul>
  <li v-click="+5">`emitTo` is a ZIO extension method</li>
  <li v-click="+6"> Question: there is another extention in action here, where ?</li>
</ul>



------

# Laminar 101

<div grid="~ cols-2 gap-4">
<div>

```scala {*|4|10|*|8|10}
import com.raquo.laminar.api.L._

val app = {
  val name = Var("World")
  div(
    input(
      placeholder := "What's your name?",
      onInput.mapToValue --> name.writer
    ),
    child.text <-- name.signal
                       .map(name => s"Hello, $name!")
  )
}
```
</div>
<div>
Basicaly Laminar provides reactive components:

<ul>
  <li v-click="+1">`Var` - a mutable value</li>
  <li v-click="+2">`Signal` - a read-only value that updates when its dependencies change</li>
</ul>

<div v-click="+3">And 2 reactive operators:</div>

<ul>
  <li v-click="+4">`-->`  Write to a Laminar reactive element</li>
  <li v-click="+5">`<--`  Read from a Laminar reactive element</li>
</ul>


</div>
</div>

<div v-click="+6">
  <h4>What is the missing part ?</h4>
  <ul>
    <li v-click="+7">`EventBus` - a way to emit and listen to events</li>
    <li v-click="+8">`EventStream` - a listen only stream</li>
  </ul>
</div>

---

# Demo Time

http://localhost:5173

---

# Laminar

<div>

```scala {1|2-3|1,9-10|18-20}
val personVar = Var(Person("John", "john.does@foo.bar", Password("notsecured") ))
val userBus  = EventBus[User]()
val errorBus = EventBus[Throwable]()
div(
  styleAttr := "max-width: fit-content;  margin-left: auto;  margin-right: auto;",
  h1("Signup"),
  div(
    styleAttr := "width: 600px; float: left;",
    // The form is generated from the case class
    personVar.asForm,
  ),
  div(
    styleAttr := "clear:both;max-width: fit-content; margin:1em auto",
    Button(
      "Create",
      disabled <-- personVar.signal.map(_.errorMessages.nonEmpty),
      onClick --> { _ =>
        PersonEndpoint
          .create(personVar.now())
          .emitTo(userBus, errorBus)
      }
    )
  )
)
```
</div>

------

# That all folks!

But ...

<div grid="~ cols-2 gap-4">
<div v-click="+1">
  <img src="./images/tellme.webp" width="70%"/>
</div>
<div>
  <ul>
    <li v-click="+1">Form ?</li>
    <li v-click="+2">Http client ?</li>
    <li v-click="+3">What about the server side ?</li>
    <li v-click="+4">What about the database ?</li>
    <li v-click="+5">What about the authentication / authorisation ?</li>
    <li v-click="+6">What about the deployment ?</li>
  </ul>
</div>
</div>



---

# Resources



* [ZIO](https://zio.dev)
* [Laminar](https://laminar.dev)
 * https://www.scala-js.org/doc/tutorial/laminar.html
* [Tapir](https://tapir.softwaremill.com)
* [ScalaJS](https://www.scala-js.org)
* [Vite](https://vitejs.dev)
* [SBT](https://www.scala-sbt.org)
* [Docker](https://www.docker.com)
* [VSCode](https://code.visualstudio.com)
* [Metals](https://scalameta.org/metals/)
