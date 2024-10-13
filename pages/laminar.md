# lklk

<div grid="~ cols-2 gap-4">
<div>

```scala
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

* `Var` - a mutable value
* `Signal` - a read-only value that updates when its dependencies change
<!-- * `EventStream` - a stream of events
* `EventBus` - a sink for events -->

And 2 reactive operators:

* `-->` - Write to a Laminar reactive element
* `<--` - Read from a Laminar reactive element


</div>
</div>

---



# Laminar s

<div>

```scala {1|2-3|8-9|17-19}
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

