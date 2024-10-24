---
theme: default
title: APIs for IoT Integration
class: text-center
transition: slide-up
---

# APIs for IoT Integration

A quick overview of how everything works together

<!-- <div class="pt-12">
  <span @click="$slidev.nav.next " class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div> -->

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
src: ./pages/intro.md
transition: fade
---

---
src: ./pages/demo.md
---

---
src: ./pages/demo-breakdown.md
---

---
src: ./pages/http-demo.md
---

---
src: ./pages/ws-demo.md
transition: fade
---

---
theme: default
title: "Quick survey 🤔"

layout: statement
---

### Quick survey 🤔

<PieChart />

---
theme: default
title: "Making sense of raw data"
---

### Making sense of raw data
___

<br />

<ul>
  <li>Learn how to work with Objects <code>{}</code> and Arrays <code>[]</code>
  </li>
</ul>
<ul class="pl-8">
  <li v-click>Loop through an Object - <code>Object.keys()</code></li>
  <li v-click>Accessing value from an Object</li>
  <li v-click>Loop through an Array - <code>.map()</code>, <code>.forEach()</code></li>
  <li v-click>Manipulating an Array - <code>.push()</code>, <code>.pop()</code>, <code>.reduce()</code></li>
  <li v-click>Validating something from an Array - <code>.includes()</code>, <code>.filter()</code></li>
</ul>

<img src="/assets/pie-chart.png" width=200 height=200 class="absolute z-2 top-[15%] right-[10%]" v-click>

```js {*}{maxHeight:'230px'}
const choicesData = familiarity.choices;
const responsesData = familiarity.responses;

console.log(choicesData); // Array(3): ["Fire! 🔥",  "Its fine 👌",  "Not much, topics are complicated 😩"]

console.log(responsesData) 
// Object: {
//   "Goerge": "Not much, topics are complicated 😩",
//   "John": "Its fine 👌",
//   "Paul": "Fire! 🔥",
//   "Ringo": "Fire! 🔥",
// }

// To render it correctly to PieChart
const data =  {
  labels: [
    "Fire! 🔥",
    "Its fine 👌",
    "Not much, topics are complicated 😩"
  ],
  datasets: [
    {
      backgroundColor: [
          "#E46651", // red
          "#41B883", // green
          "#00D8FF" // blue
      ],
      data: [2, 1, 1]
    }
  ]
}

// HOW????
```
---
src: ./pages/authentication.md
---

---
layout: end
---

# Thank you! 🙏