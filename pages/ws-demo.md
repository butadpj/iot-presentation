---
theme: default
title: Websockets
class: text-left
---

### Websockets
___

```js {*}{maxHeight:'60vh'}
const WebSocket = require("ws");

// Create a WebSocket server listening on port 4000
const websocketServer = new WebSocket.Server({ port: 4000 });

let sensors = [
  {
    user: "paul",
    enabled: false,
    value: 100,
  },
];

websocketServer.on("connection", (ws) => {
  console.log("New client connected");

  // Send a welcome message when a client connects
  ws.send(JSON.stringify({ message: "Welcome to the WebSocket server!" }));

  // Receive messages from the client
  ws.on("message", (data) => {
    const message = JSON.parse(data);
    console.log("Received:", message);

    const { action, payload } = message;

    if (action === "GET_ALL_SENSORS") {
      ws.send(
        JSON.stringify({
          action: "GET_ALL_SENSORS",
          data: sensors,
        })
      );
    } else if (action === "ADD_SENSOR") {
      sensors.push(payload);
      ws.send(JSON.stringify({ message: "New sensor added" }));

      broadcastToAllClients({
        action: "GET_ALL_SENSORS",
        data: sensors,
      });
    } else if (action === "UPDATE_SENSOR") {
      const userToBeUpdated = payload.user; // Get the user to be updated from request parameters
      const newData = payload.newData;

      if (sensors.find((sensor) => sensor.user === userToBeUpdated)) {
        // perform the update if the user to be updated exists
        sensors = sensors.map((sensor) => {
          if (sensor.user === userToBeUpdated) {
            return { ...sensor, ...newData };
          }
          return sensor;
        });

        ws.send(JSON.stringify({ message: "Sensor updated" }));
        broadcastToAllClients({
          action: "GET_ALL_SENSORS",
          data: sensors,
        });
      } else {
        // otherwise, do nothing
        ws.send(
          JSON.stringify({
            message: "User to be updated doesn't exists",
          })
        );
      }
    } else if (action === "DELETE_SENSOR") {
      const userToBeDeleted = payload.user; // Get the user to be updated from request parameters

      if (sensors.find((sensor) => sensor.user === userToBeDeleted)) {
        sensors = sensors.filter((sensor) => sensor.user !== userToBeDeleted);
        ws.send(
          JSON.stringify({
            message: `${userToBeDeleted} has successfully deleted`,
          })
        );
        broadcastToAllClients({
          action: "GET_ALL_SENSORS",
          data: sensors,
        });
      } else {
        ws.send(
          JSON.stringify({
            message: `User to be deleted doesn't exists`,
          })
        );
      }
    }
  });

  // Handle when the client disconnects
  ws.on("close", () => {
    console.log("Client disconnected");
  });
});

// Broadcast a message to all connected clients
const broadcastToAllClients = (data) => {
  websocketServer.clients.forEach((client) => {
    if (client.readyState === WebSocket.OPEN) {
      client.send(JSON.stringify(data));
    }
  });
};

console.log("WebSocket server is running on ws://localhost:4000");
```
<br />

<WebsocketsDemo />


<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>
