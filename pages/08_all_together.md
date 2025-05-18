
# Signup page


```scala {*|1|2-3|8-9|17-19}
val personVar = Var(Person("John", "john.does@foo.bar", Password("notsecured") ))
val userBus  = EventBus[User]()
val errorBus = EventBus[Throwable]()
div(
  h1("Signup"),
  div(
    styleAttr := "float: left;",
    // The form is generated from the case class
    personVar.asForm,
  ),
  div(
    styleAttr := "max-width: fit-content; margin:1em auto",
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


---

# Profile page

```scala {*|5-7|9|11-17}
object ProfilePage:

  val userBus = new EventBus[User]

  def apply(): ReactiveHtmlElement[HTMLDivElement] = div(
    child <-- session(withoutSession)(withSession)
  )

  def withoutSession = h1("Please log in to view your profile")

  def withSession(userToken: UserToken) = div(
    onMountCallback { _ =>
      UserEndpoint.profile(()).emitTo(userBus)
    },
    renderUser
  )

  def renderUser = div(
      h1("Profile Page"),
      child <-- userBus.events.map { user =>
        div(
          cls := "srf-form",
          h2("User"),
          div("First name: ", user.firstname),
          div("Last name: ", user.lastname),
          div("Email: ", user.email)
        )
      }
    )
  


```

