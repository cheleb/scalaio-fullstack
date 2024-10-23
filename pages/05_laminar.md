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
<div v-click="+8">
  <h4>Demo</h4>
  <a href="http://localhost:5173">Demo signup page</a>
</div>

---

<div>

```scala {*|1|2-3|9-10|18-20}
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

