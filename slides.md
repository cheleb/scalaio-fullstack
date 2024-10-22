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

# Scala Full Stack

<ul>
  <li v-click="+1">Scala JS</li>
  <li v-click="+2">Scala JVM</li>
  <li v-click="+3">Scala Native</li>
  <li v-click="+4">Scala Wasm</li>
</ul>

---
src: ./pages/01_ono-at-ledger.md
---

---
src: ./pages/02_why.md
---

---


# Agenda

<div grid="~ cols-[50%_50%] gap-1">
  <div>

````md magic-move
```scala

object ScalaFullStack:
  
  object SetUp

  object Projects:

     object Frontend

     object Backend 

```

```scala

object ScalaFullStack:
  
  object SetUp:

    val build = ("sbt", "npm/Vite", "Docker")

    val ide = "VSCode / Metals 🤘🏼"

    val scalfold = "sbt new cheleb/zio-scalajs-laminar.g8"

  object Projects:

     object Frontend

     object Backend 

```

````
</div>
  <div>
    <img v-click="+2" src="./images/runtime-stack.svg" style="width: 50%; height: auto;" />
  </div>
</div>

---
src: ./pages/03_setup.md
---

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
   <img v-click="+2" src="./images/tapir-zio.png" style="width: 100%;" />
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
src: ./pages/04_tapir.md
---

---

# Laminar 101

<div grid="~ cols-2 gap-3">
<div>

```scala {*|4|10|||8|10}
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
  <li v-click="+3">`EventStream` - a stream of events</li>
  <li v-click="+3">`EventBus` - a stream of events that can be emitted to</li>
</ul>

<div v-click="+4">And 2 reactive operators:</div>

<ul>
  <li v-click="+5">`-->`  Write to a Laminar reactive element</li>
  <li v-click="+6">`<--`  Read from a Laminar reactive element</li>
</ul>


</div>
</div>

<div v-click="+7">
  <h4>In action</h4>
  <a href="https://demo.laminar.dev/app/basic/hello">https://demo.laminar.dev/app/basic/hello</a>
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
  <li v-click="+4">`emitTo` is another extension method, we will detail shortly</li>
  <li v-click="+5"> Question: there is another extention in action here, where ?</li>
</ul>

---

# Tapir 101

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

---

# Demo Time

http://localhost:5173

---

# Laminar

<div>

```scala {*|1|2-3|1,9-10|18-20}
val personVar = Var(Person("John", "john.does@foo.bar", Password("notsecured") ))
val userBus  = EventBus[User]()
val errorBus = EventBus[Throwable]()
div(
  styleAttr := "max-width: fit-content;  margin-left: auto;  margin-right: auto;",
  h1("Signup"),
  div(
    styleAttr := "float: left;",
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

# Under the hood - Laminar Form Derivation

No boilerplate, no magic, just Scala.

<div v-click="+1"><a href="https://cheleb.github.io/laminar-form-derivation/demo/index.html">Laminar Form Derivation</a> with Magnolia</div>
<ul>
  <li v-click="+2">Case class to form</li>
  <li v-click="+3">Databinding</li>
  <li v-click="+4">Validation</li>
  <li v-click="+5">Error handling</li>
</ul>

<div v-click="+6">

```scala
import import dev.cheleb.scalamigen.*

case class Person(name: String, email: String, password: Password)

val personVar = Var(Person("John", "john.does@foo.bar", Password("notsecured") ))

personVar.asForm

```

</div>

------

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
</ul>

<div v-click="+8" class="absolute left-30%">
```scala
PersonEndpoint
          .create(personVar.now())
          .emitTo(userBus, errorBus)
```
</div>

---

# Under the hood - Http Request

Inspired from [Rock The JVM](https://rockthejvm.com) courses/blog/videos ZIO rite of passage.

<div v-click="+1">
```scala
import dev.cheleb.ziolaminartapir.*
```
</div>
<div v-click="+2">
Et voilà, you can now use Tapir in a reactive way, in you ScalaJS project.
</div>

<div v-click="+3">
```scala
// This import brings bunch of extension to Endpoint and ZIO.
```
</div>

<div v-click="+4">

````md magic-move {at:5}

```scala
extension [I, E <: Throwable, O](endpoint: Endpoint[Unit, I, E, O, Any])
```

```scala
extension [I, E <: Throwable, O](endpoint: Endpoint[Unit, I, E, O, Any])
  /** Call the endpoint with a payload, and get a ZIO back.
    * @param payload
    * @return
    */
  def apply(payload: I): RIO[SameOriginBackendClient, O] =
    ZIO
      .service[SameOriginBackendClient]
      .flatMap(_.endpointRequestZIO(endpoint)(payload))
```
````
</div>


---

# Under the hood - Http Request

<div v-click="+1">

```scala
private[ziolaminartapir] abstract class BackendClient(
    backend: SttpBackend[Task, ZioStreamsWithWebSockets],
    interpreter: SttpClientInterpreter
):

  private[ziolaminartapir] def endpointRequestZIO[I, E <: Throwable, O](
        baseUri: Uri,
        endpoint: Endpoint[Unit, I, E, O, Any]
    )(
        payload: I
    ): ZIO[Any, Throwable, O] =
      backend
        .send(endpointRequest(baseUri, endpoint)(payload))
        .map(_.body)
        .absolve
```
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
