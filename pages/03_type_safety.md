# Scala type system

### Rich type system

<div grid="~ cols-[30%_70%] gap-2">

<div>
<v-clicks depth="3">
  
  - Generic variance
      - covariant
      - contravariant
      - invariant
    - union types
    - intersection types
    - higher-kinded types
    - type classes
  - Compile time operations
    - context functions
    - extension
    - macros
    - metaprogramming
</v-clicks>

</div>

<div style="margin-top: 4em">

````md magic-move {lines: true, at:1}
```scala
trait Animal
trait Dog extends Animal
trait Cat extends Animal 
```
```scala
// Generic types
trait List[+A]
trait Vet[-A]
```

```scala
trait List[+A] // is co-variant "+"

val animals: List[Animal] = List.empty[Dog]
```
```scala
trait Vet[-A] // is contra-variant "-"

val veterinary: Vet[Dog] = Vet[Animal]("Dr. Who") // Contra-variant
```
```scala
val java.util.List[Animal] = new java.util.ArrayList[Dog]() 💥
```

```scala
type pet = Cat | Dog 
```
```scala
type Environement = Database & RabbitMQ 
```
```scala
trait Functor[F[_]] extends Invariant[F]: 
  def map[A, B](fa: F[A])(f: A => B): F[B]
```

````
</div>
</div>




<!-- 
</div>

<div v-click="[6,7]" v-motion style="position:absolute" duration="1s" delay="4s"
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 350, y: -380 }"
  :leave="{ x: 350 }"
>
  Type safety.
    <img src="../images/smart.png" style="width: 50%; height: auto" />
</div>
 Rich type system and facilities around it:
-->