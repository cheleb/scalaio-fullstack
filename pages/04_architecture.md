# The App



<img src="../images/architecture-global.drawio.svg" style="width: 60%; height: auto;" />

<div v-click="+6" v-motion style="position:absolute" duration="1s"
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 350, y: -360 }"
  :leave="{ x: 50 }"
>
 <img src="../images/zio.drawio.svg" style="width: 80%; height: auto;" />
</div>

<div v-click="[2,6]" v-motion style="position:absolute" duration="1s"
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-2="{ x: 110, y: -360 }"
  :click-3="{ x: 110, y: -300 }"
  :click-4="{ x: 110, y: -235 }"
  :click-5="{ x: 110, y: -175 }"
  :leave="{ x: 110 }"
>
  <img src="../images/object.svg" style="width: 40%; height: auto;" />
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

<img src="../images/architecture.svg" style="width: 100%" />
<img src="../images/youarehere.png" width="50"  v-motion duration="300ms" 
  :initial="{ x: 0, y:0 }"
  :enter="{ x: 350, y:-490 }"
 />


 <!-- 
 
 Little bit simplified, but the idea is here.

 The 'you are here' will help to navigate in the architecture.

 -->


