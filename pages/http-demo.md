---
theme: default
title: HTTP in details
class: text-left

transition: slide-left
---

### HTTP in details
___

```js {*}{maxHeight:'60vh'}
const express = require("express");
const app = express();
const cors = require("cors");

app.use(cors()); // Make this server accessible by other applicationss
app.use(express.json()); // Allow server to parse json contents from the body

let sensors = [
  {
    user: "paul",
    enabled: false,
    value: 100,
  },
];

// Retrieve all sensors
app.get("/api/sensors", (request, response) => {
  response.json(sensors);
});

app.post("/api/sensors", (request, response) => {
  const newData = request.body; // Get the new data from request's body
  sensors.push(newData);
  response.send("New data added");
});

app.put("/api/sensors/:user", (request, response) => {
  const userToBeUpdated = request.params.user; // Get the user to be updated from request parameters
  const newData = request.body; // Get user's new data

  if (sensors.find((sensor) => sensor.user === userToBeUpdated)) {
    // perform the update if the user to be updated exists
    sensors = sensors.map((sensor) => {
      if (sensor.user === userToBeUpdated) {
        return { ...sensor, ...newData };
      }
      return sensor;
    });

    response.send("New data added");
  } else {
    // otherwise, do nothing
    response.send("User to be updated doesn't exists");
  }
});

app.delete("/api/sensors/:user", (request, response) => {
  const userToBeDeleted = request.params.user; // Get the user to be deleted from request parameters
  if (sensors.find((sensor) => sensor.user === userToBeDeleted)) {
    sensors = sensors.filter((sensor) => sensor.user !== userToBeDeleted);
    response.send(`${userToBeDeleted} has successfully deleted`);
  } else {
    response.send(`User to be deleted doesn't exists`);
  }
});

// Start server on port 3000
app.listen(3000, () => {
  console.log(`Server running on http://localhost:3000`);
});
```
<br />

<HTTPDemo />


<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>
