---
transition: fade
layout: two-cols
---

## Metaprog / Generic derivation

Where ?

<img src="../images/architecture-global.drawio.svg" alt="Architecture Diagram" width="100%"/>

<div v-click="[1,6]" v-motion style="position:absolute" duration="1s"
  :initial="{ x: 0, y: -100 }"
  :enter="{ x: 210, y: -360 }"
  :click-1="{ x: 310, y: -300 }"
  :click-2="{ x: 310, y: -300 }"
  :click-3="{ x: 310, y: -250 }"
  :click-4="{ x: 310, y: -145 }"
  :leave="{ x: 310 }"
>
  <img alt="obj" src="../images/object.svg" style="width: 40%; height: auto;" />
</div>


::right::

<div v-click="[1,4]" v-motion style="position:absolute" duration="1s"
  :initial="{ x: 0, y: 0 }"
  :enter="{ x: 0, y: 0 }"
  :click-1="{ x: 100, y: 100 }"
  :click-2="{ x: 310, y: -240 }"
  :click-3="{ x: 310, y: -175 }"
  :leave="{ x: 310 }"
>
<img alt="text form" src="../images/form/LabelForm.png" style="width: 20%; height: auto;" />
<br />
<img alt="text form" src="../images/form/StringForm.png" style="width: 20%; height: auto;" />
<br />
<img alt="text form" src="../images/form/PasswordForm.png" style="width: 20%; height: auto;" />
</div>
<div v-click="[1,6]" v-motion style="position:absolute" duration="1s"
  :initial="{ x: 0, y: -200 }"
  :enter="{ x: 100, y: -200 }"
  :click-2="{ x: 200, y: 100 }"
  :click-3="{ x: 310, y: -240 }"
>
<img alt="text form" src="../images/UI5_bindings.png" style="width: 40%; height: auto;" />
</div>

<br /><br /><br /><br /><br />


<div v-click="+3">
````md magic-move {at:3}
```
- Form
```
```
- Form
- HTTP
  - JsonCodec
  - OpenAPI
  - GraphQL
```
```
- Form
- HTTP
  - JsonCodec
  - OpenAPI
  - GraphQL
- SQL
- Avro
- Protobuf
- Config...
```
````
</div>
