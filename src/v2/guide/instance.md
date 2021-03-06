---
title: อินสแตนส์ Vue
type: guide
order: 3
---

## สร้างอินสแตนส์ Vue

ทุกๆ แอพพลิเคชัน Vue จะเริ่มต้นโดยสร้าง **อินสแตนส์ของ Vuew** ใหม่ด้วยฟังก์ชัน `Vue`:

```js
var vm = new Vue({
  // ตัวเลือก
})
```

แม้ว่าไม่เกี่ยวข้องกับ [MVVM pattern](https://en.wikipedia.org/wiki/Model_View_ViewModel) แต่การออกแบบของ Vue ก็ได้แรงบันดาลใจจากแพทเทิร์นนี้ ดังกติการ่วมกัน เราจะใช้ตัวแปร `vm` (ย่อมาจาก ViewMode) เพื่ออ้างอิงถึงอินสแตนส์ของ Vue

เมื่อสร้างอินสแตนส์ของ Vue คุณส่งผ่าน **ตัวเลือกวัตถุ (options object)** ส่วนใหญ่ของคำแนะนำนี้จะอธิบายว่าคุณสามารถใช้งานตัวเลือกเหล่านี้อย่างไร เพื่อสร้างพฤติกรรมของแอพพลิเคชันที่คุณต้องการ สำหรับการอ้างอิง คุณสามารถดูได้ที่รายการเต็มของตัวเลือกใน [รายการอ้างอิง API](../api/#Options-Data)

แอพพลิเคชันของ Vue ประกอบด้วย **อินสแตนส์หลัก Vue** สร้างด้วยคำสั่ง `new Vue`, ตัวเลือกจัดอยู่ในรูปต้นไม้ที่ซ้อนกัน คอมโพแนนต์ที่นำกลับมาใช้ใหม่ ตัวอย่างเช่น แอพพลิเคชัน Todo โครงสร้างต้นไม้ของคอมโพแนนมีรูปแบบดังนี้:

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

เราจะกล่าวถึงรายละเอียดของ [ระบบคอมโพแนนต์](components.html) ในภายหลัง สำหรับตอนนี้รู้เพียงว่าคอมโพแนนต์ของ Vue ทั้งหมดเป็นอินสแตน์ของ Vue และยอมรับตัวเลือกวัตถุเดียวกัน (ยกเว้นแต่บางตัวเลือกเฉพาะหลัก)

## ข้อมูลและเมธอด

เมื่ออินสแตนส์ Vue สร้างขึ้น มันจะเพิ่มคุณสมบัติทั้งหมดที่เจอในวัตถุ `data` ไปยัง **ระบบตอบสนองทันที** ของ ​Vue เมื่อค่าของคุณสัมบัติของวัตถุเปลี่ยนแปลง การแสดงผลจะ "ตอบสนอง" ต่อการเปลี่ยนแปลงดังกล่าวและอัพเดตด้วยค่าใหม่

```js
// วัตถุข้อมูลของเรา
var data = { a: 1 }

// วัตถุที่เพิ่มไปยังอินสแตนส์ Vue
var vm = new Vue({
  data: data
})

// รับเอาคุณสมบัติบนอินสแตนส์
// ส่งกลับข้อมูลจากข้อมูลต้นฉลับ
vm.a == data.a // => true

// เปลี่ยนแปลงค่าของคุณสมบัติบนอินสแตนส์
// โดยจะมีผลต่อข้อมูลต้นฉบับ
vm.a = 2
data.a // => 2

// ... และในทางกลับกัน
data.a = 3
vm.a // => 3
```

เมื่อข้อมูลเปลี่ยนแปลง การแสดงผลจะสร้างการแสดงผลใหม่ อาจจะกล่าวได้ว่าคุณสมบัติใน `data` เป็นเพียง **การตอบสอนงทันที** ถ้าวัตถุดังกล่าวมีอยู่ตั้งแต่อินสแตนส์ถูกสร้างขึ้น นั้นหมายความว่าถ้าเราเพิ่มคุณสมบัติใหม่ แบบนี้:

```js
vm.b = 'hi'
```

ดังนั้นการเปลี่ยนแปลงของ `b` จะไม่มีผลกระทบทำให้การแสดงผลเปลียนแปลง ถ้ารู้ว่าต้องการเพิ่มคุณสมบัติในภายหลัง จะต้องใส่คุณสมบัติดังกล่าวไป ด้วยค่าว่างเปล่าหรือค่าเริ่มต้น เพื่อใช้ในภายหลัง ตัวอย่างเช่น:

```js
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

มีข้อยกเว้นเพียงใช้คำสั่ง `Object.freeze()` ซึ่งจะป้องกันคุณสมบัติที่มีอยู่ของวัตถุไม่ให้ถูกเปลี่ยนแปลง นั้นก็หมายความว่าระบบตอบสนองทันทีจะไม่ _ติดตาม_ การเปลี่ยนแปลงที่เกิดขึ้น

```js
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- this will no longer update `foo`! -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

นอกเหนือจากคุณสมบัติของข้อมูลแล้ว อินสแตนส์ Vue ยังมีคุณสมบัติและเมธอดของอินสแตนส์ที่มีประโยชน์อีกจำนวนหนึ่ง ซึ่งจะมีคำนำหน้าเป็น `$` ที่จะแตกต่างจากคุณสมบัติที่ผู้ใช้กำหนดเอง ดังตัวอย่าง:

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch เป็นเมธอดของอินสแตนส์
vm.$watch('a', function (newValue, oldValue) {
  // callback นี้จะถูดเรียกเมื่อ `vm.a` เปลี่ยนแปลง
})
```

ซึ่งสามารถดูรายละเอียดเพิ่มเติมสำหรับรายการเต็มของคุณสมบัติอินสแตนส์และเมธอทได้ที่ [รายการอ้างอิง API](../api/#Instance-Properties) 

## ตะขอวงจรชีวิตของอินสแตนส์

แต่ล่ะอินสแตนส์ Vue ผ่านชุดของขั้นตอนการเริ่มต้นเมื่ออินสแตนส์ถูกสร้างขึ้น - ตัวอย่างเช่น อินสแตนส์จำเป็นต้องตั้งค่าการเฝ้าตรวจสอบข้อมูล, รวบรวมเทมเพลต, ติดตั้งอินสแตนส์ไปที่ DOM และอัพเดต DOM เมื่อข้อมูลมีการเปลี่ยนแปลง ในระหว่างทางอินสแตนส์จะเรียกฟังก์ชันที่เรียกว่า **ตะขอวงจรชีวิต** ที่ให้โอกาสผู้ใช้งานเพิ่มโค้ดของตัวเองที่ขั้นตอนเฉพาะ

ตัวอย่างเช่น, ตะขอ [`created`](../api/#created) สามารถใช้เพื่อรันโค้ดหลังจากที่อินสแตนส์ถูกสร้างขึ้น

```js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` points to the vm instance
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

ยังมีตะขออื่นๆ ซึ่งจะเรียกที่ขั้นตอนที่แตกต่างกันของวงจรชีวิตของอินสแตนส์ ดังเช่น [`mounted`](../api/#mounted), [`updated`](../api/#updated), และ [`destroyed`](../api/#destroyed) ตะขอวงจรชีวิตทั้งหมดจะถูกเรียกเมื่อบริบท `this` ชี้ไปยังอินสแตนส์ Vue ที่ร้องขอมัน

<p class="tip">ห้ามใช้ [arrow functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) กับคุณสมบัติตัวเลือกหรือ callback ดังเช่น `created: () => console.log(this.a)` หรือ `vm.$watch('a', newValue => this.myMethod())` เมื่อ arrow functions จะผูกไว้กับบริบทแม่ `this` ไม่ใช่อินสแตนส์ Vue ดังที่คุณคิด ส่งผลให้เกิดข้อผิดพลาดเสมอ ดังเช่น  `Uncaught TypeError: Cannot read property of undefined` หรือ `Uncaught TypeError: this.myMethod is not a function`.</p>

## แผนภาพวงจรชีวิต

ด้านล่างนี้เป็นแผนภาพสำหรับวงจรชีวิตของอินสแตนส์ ไม่จำเป็นต้องเข้าใจทั้งหมดทุกสิ่งทุกอย่างในตอนนี้ แต่เพียงแค่ทำความเข้าใจและลองทำดู จะเป็นการอ้างอิงที่มีประโยชน์

![The Vue Instance Lifecycle](/images/lifecycle.png)
