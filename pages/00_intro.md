# Scala Full Stack


<h2>Complexity</h2>
<hr style="margin: 1em"/> 
<v-clicks depth="2">

  * Is scala a complex language?
    - Yes
    - But can/should be used in a simple way.
</v-clicks>
<div style="margin: 2em" v-motion v-click
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 400 }"
  :leave="{ x: 50 }">
<img style="margin: 2em" src="../images/power.jpg" width="20%" />
Principle of Least Power
</div>

<!-- 

- Scala is a complex language, but it is also a language that can be used in a simple way.
- The libraries around Scala are complex, but they are also libraries that can be used in a simple way.
- The tooling around Scala is complex.

 -->

---

# Scala Full Stack


<h2>Complex, but Safer</h2>
<hr style="margin: 1em"/> 

<v-clicks depth="2">

  * Tame the complexity with types.
    - Type safety 101.
    - Algebraic Data Type.
    - Generic Derivation.
</v-clicks>

<!-- 

- Type safety means that you can catch errors at compile time.

 -->

---

# Scala Full Stack

## Type Safety

<div grid="~ cols-[37%_62%]">
<v-clicks depth="2" style="margin: 2em">

  * Compiler is your friend. 
    - <span v-mark="{type:'underline', color:'orange', at:17}">Catch errors early</span>.
    - Refactor with confidence.
  * But:
    * <span v-mark="{type:'underline', color:'orange', at:18}">Boilerplate</span>
    * <span v-mark="{type:'underline', color:'orange', at:19}">Boxing / Unboxing</span>
</v-clicks>
<div>

````md magic-move

```scala
//
```

```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int)
```

```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int)
// 1000 lines later
val user = User("John",
                "Doe",
                "john.does@foo.bar",
                "notsecured",
                42)
```

```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int)
// 1000 lines later
val user = User(💥"Doe",
                💥"John",
                 "john.does@foo.bar",
                 "notsecured",
                 42)
```

```scala
case class User(firstname: String,
                lastname: String,
                email: String,
                password: String,
                age: Int)
// 1000 lines later
val user = User(💥"Doe",
                💥"john.does@foo.bar",
                💥"notsecured",
                💥"John",
                💥-42)
```
```scala
case class User(firstname: Firstname,
                lastname: Lastname,
                email: Email,
                password: Password,
                age: Age)
```
```scala {*|1,7,13|2,8,13|3,9,13|4,10,13}
case class User(firstname: Firstname,
                lastname: Lastname,
                email: Email,
                password: Password,                
                age: Age)
// 1000 lines later
val firstname = Firstname("John")
val lastname = Lastname("Doe")
val email = Email("john.doe@foo.far")
val password = Password("notsecured")
val age = Age(42)
// Few lines later
val user = User(firstname, lastname, email, password, age)
```

````

</div>

</div>

<!-- 

- The compiler is your friend, it will:
  - catch errors early.
  - allow refactor your code with confidence.
- But it comes with a cost:
  - boilerplate, you have to write more code.
  - Boxing / Unboxing, you have to convert your data to the right type.

 -->

---

# Scala Full Stack

## Boilerplate, really ?

<div grid="~ cols-[40%_60%]">
<v-clicks depth="2" style="margin: 2em">

  * Not at all.
    - Just added validation step.
  * But:
    * <span v-mark="{type:'underline', color:'orange', at:14}">Boxing / Unboxing</span>
</v-clicks>
<div>

````md magic-move

```scala
//
```

```scala {*|1|3|4|5}
case class Email(value: String)
object Email:
  def attempt(value: String): Either[String, Email] =
    if value.contains("@") then Right(Email(value))
    else Left("Invalid email")
```
```scala
val user = for
  firstname <- Firstname.attempt("John")
  lastname <- Lastname.attempt("Doe")
  email <- Email.attempt("john.does@foo.bar")
  password <- Password.attempt("notsecured")
yield User(firstname, lastname, email, password, Age(42))
```
```scala
val user: Either[String, User] = for
  firstname <- Firstname.attempt("John")
  lastname <- Lastname.attempt("Doe")
  email <- Email.attempt("john.does@foo.bar")
  password <- Password.attempt("notsecured")
yield User(firstname, lastname, email, password, Age(42))
```
````
</div>
</div>

<div v-motion v-click 
  :initial="{  y: 100, x:300 }"
  :enter="{  y: 0 }" style="border: 1px #FF9999 solid; width:20em">
“make illegal states unrepresentable” 
<br />
-- Yaron Minsky
</div>


<!-- 

We just want to handle and validate the data, before using it.

-->



---

# Scala Full Stack

## Boxing / unboxing, really ?

<div grid="~ cols-[40%_60%]">
<v-clicks depth="2" style="margin: 2em">

  * Not at all.
    - Just compilation garanties.
</v-clicks>
<div>

````md magic-move

```scala
//
```

```scala
case class Email(value: String)
object Email:
  def attempt(value: String): Either[String, Email] =
    if value.contains("@") then Right(Email(value))
    else Left("Invalid email")
```
```scala {*|1|4,5}
opaque type Email = String
object Email:
  def attempt(value: String): Either[String, Email] =
    if value.contains("@") then Right(value)
    else Left("Invalid email")
```
````
<v-click>
Strickly same usage:
```scala
val user: Either[String, User] = for
  firstname <- Firstname.attempt("John")
  lastname <- Lastname.attempt("Doe")
  email <- Email.attempt("john.does@foo.bar")
  password <- Password.attempt("notsecured")
yield User(firstname, lastname, email, password, Age(42))
```
</v-click>

</div>
</div>



<div v-motion v-click 
  :initial="{  y: 100, x:300 }"
  :enter="{  y: 50 }" style="border: 1px #FF9999 solid; width:20em">
Type safe is a compilation garanty
  <br />
  With no runtime overhead.
<br />
</div>
