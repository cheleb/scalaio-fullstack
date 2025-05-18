# Architecture / Applications

<img alt="browser" src="../images/arch-browser.svg" style="width: 70%; height: auto; margin: 1em" />
<img alt="jvm" src="../images/arch-jvm.svg" style="width: 70%; height: auto;; margin: 1em; margin-top: 1em" />
<img alt="jvm" src="../images/arch-laminar.svg" v-click="+1" v-motion style="position:absolute; width: 40%; height: auto; margin: 1em" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 150, y: -360 }"
/>
<img alt="tapir" src="../images/arch-tapir.svg" v-click="+2" v-motion style="position:absolute; width: 40%; height: auto; margin: 1em" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 150, y: -285 }"
/>
<img alt="service" src="../images/arch-business.svg" v-click="+3" v-motion style="position:absolute; width: 40%; height: auto; margin: 1em" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 150, y: -210 }"
/>
<img alt="service" src="../images/arch-persistance.svg" v-click="+4" v-motion style="position:absolute; width: 40%; height: auto; margin: 1em" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 150, y: -130 }"
/>
<img alt="service" src="../images/arch-jdbc.svg" v-click="+5" v-motion style="position:absolute; width: 40%; height: auto; margin: 1em" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 150, y: -50 }"
/>

<img alt="service" src="../images/database.drawio.svg" v-click="+6" v-motion style="position:absolute; width: 8%; height: auto; margin: 1em" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 280, y: -0 }"
/>

  
 <img v-click="+7" v-motion  alt="zio" src="../images/zio.drawio.svg" style="position:absolute; width: 10%; height: auto;" :initial="{ x: 0, y: -100 }"
  :enter="{ x: 500, y: -315 }"
  />


<img v-click="[8,14]" v-motion alt="obj" src="../images/object.svg" style="position:absolute; width: 3%; height: auto;" :initial="{ x: 0, y: -100 }"
  :click-8="{ x: 430, y: -335 }"
  :click-9="{ x: 430, y: -255 }"
  :click-10="{ x: 430, y: -185 }"
  :click-11="{ x: 430, y: -105 }"
  :click-12="{ x: 430, y: -35 }"
  :click-13="{ x: 320, y: 25 }"
  :leave="{ x: 50 }"
  />

<!--

Very classical layered architecture, with a a shared Transports Layer (API and Payloads)

- The UI is a Laminar app
- REST API with Tapir
- Business logic in the domain layer
- Persistence with Quill
 
And
- ZIO for the effect system

-->
