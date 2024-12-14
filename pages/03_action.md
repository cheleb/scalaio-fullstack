# ZIO Rite of Passage 


````md magic-move {at: 2}
```text
The good: 
  👉 DX awesome, feedback loop
  - Integration: UI, Effect System, HTTP client/server
The bad:
  - Integration: Quite a few lines of code.
The Ugly:
  - Setup a new project
  - Start development environment
```
```text
The good: 
  - DX awesome, feedback loop
  👉 Integration: UI, Effect System, HTTP client/server
The bad:
  - Integration: Quite a few lines of code.
The Ugly:
  - Setup a new project
  - Start development environment
```
```text
The good: 
  - DX awesome, feedback loop
  - Integration: UI, Effect System, HTTP client/server
The bad:
  👉 Integration: Quite a few lines of code.
The Ugly:
  - Setup a new project
  - Start development environment
```
```
The good: 
  - DX awesome, feedback loop
  - Integration: UI, Effect System, HTTP client/server
     - zio-tapir-laminar.jar
The Ugly:
  - Setup a new project
  - Start development environment
```
```
The good: 
  - DX awesome, feedback loop
  - Integration: UI, Effect System, HTTP client/server
     - zio-tapir-laminar.jar
  - g8 scaffold
The Ugly:
  - Start development environment
```
```
The good: 
  - DX awesome, feedback loop
  - Integration: UI, Effect System, HTTP client/server
     - zio-tapir-laminar.jar
  - g8 scaffold
  - VSCode automation
```
````

<div v-click="[1, 2]"  style="position:absolute"  v-motion
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 400 }"
  :leave="{ x: 50 }">
  DX: Developer Experience:

  * predictability & testability
  * quick feedback loop (FE && BE)
</div>

<div v-click="[2, 3]"  style="position:absolute"  v-motion
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 400, y: -200 }"
  :leave="{ x: 50 }">
  <img src="../images/integration.drawio.svg" alt="ZIO" style="width: 50%; height: auto;" />
</div>


<!--

* DX: Developer Experience:
  * predictability & testability are inherent to the FP paradigm
  * quick feedback loop is essential for productivity
    * Incremental compilation (Scala JS / JVM)
    * hot reload of UI Frontend (Vite)
    * warm reload of Backend (SBT)
* Integration: UI, Effect System, HTTP client/server
  * ZIO <-> Laminar
  * ZIO <-> Tapir
  * ZIO <-> Quill (Database)


* Integration: Quite a few lines of code.
  * extraction to an external library
* I'm not going to lie, the setup is quite a few lines of code.
  * extraction to a scaffold template

-->