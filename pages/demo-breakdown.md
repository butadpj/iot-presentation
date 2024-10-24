---
theme: default
title: Demo breakdown
class: text-left

layout: statement
transition: fade
---

### Let's break things down

---
theme: default
title: Demo breakdown

layout: image
backgroundSize: contain
image: /assets/overview.png
---


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
theme: default
title: APIs from the Demo
class: text-left
transition: slide-left
---

### APIs from the Demo
___

```js
import { initializeApp } from "firebase/app";
import { getDatabase } from "firebase/database";

const app = initializeApp(firebaseConfig); // Connect to firebase server
const database = getDatabase(app); // Connect to firebase realtime database
```
<br />

```js
import { ref, onValue, update } from "firebase/database";
import { database } from "/utils/firebase";
                
// 👇 Retrieving data from the database
onValue(ref(database, "sensors"), (snapshot) => {
  const sensors = snapshot.val(); // sensors data
});

//👇 Updating data on the database
update(ref(database), updates)
  .then(() => {
    console.log(`Sound level (${soundLevel}) saved to Firebase.`);
  })
  .catch((err) => console.error("Error saving to Firebase:", err));
```


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
theme: default
title: Other form of APIs
class: text-left

transition: fade
---

### Other form of APIs
___

```js
const express = require('express');
const app = express();
const cors = require("cors");

app.use(cors());

const sensors = [
  {
    user: "paul",
    enabled: false,
    value: 100,
  },
];

// API endpoint
app.get("/api/sensors", (req, res) => {
  res.json(sensors);
});

// Start server on port 3000
app.listen(3000, () => {
  console.log(`Server running on http://localhost:3000`);
});
```
<br />

<FetchFromServer />


<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>
