# The App



<img src="../images/architecture-global.drawio.svg" style="width: 60%; height: auto;" />

<div v-click v-motion style="position:absolute" duration="1s"
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 350, y: -380 }"
  :leave="{ x: 50 }"
>
 <img src="../images/zio.drawio.svg" style="width: 80%; height: auto;" />
</div>


<!--

Very classical layered architecture, with a a shared Transports Layer (API and Payloads)

- The UI is a Laminar app
- REST API with Tapir
- Business logic in the domain layer
- Persistence with Quill
 
And
- ZIO for the effect system


-->


---

# The App architecture

<img src="../images/architeture-details.drawio.svg" style="width: 90%; height: auto;" />


<!--

With more details, we can see the layers of the app.

But here I want to explicit the peer to peer layers between the UI and the backend.

Botton up:
* Transport Layer
  * Low level HTTP client and server (Fetch, Netty)
  * Sttp client and server abstraction.
* API Layer
  * Tapir
  * Server Controller
  * Client 
* UI and Business Logic

-->

---

<img src="../images/architecture.svg" style="width: 100%" />
<img src="../images/youarehere.png" width="50"  v-motion duration="300ms" 
  :initial="{ x: 0, y:0 }"
  :enter="{ x: 350, y:-490 }"
 />


 <!-- 
 
 Little bit simplified, but the idea is here.

 The 'you are here' will help to navigate in the architecture.

 -->


