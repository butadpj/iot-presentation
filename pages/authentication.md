---
theme: default
title: Setting up authentication

layout: image
backgroundSize: contain
image: /assets/token-based-auth.png
---


---
theme: default
title: Setting up authentication
class: text-left
---

### Setting up authentication
___

```js {*}{maxHeight:'15vh'}
// middleware.js
const jwt = require("jsonwebtoken");

const secretKey = "hard-to-guess-characters"; // normally, this is saved in a .env file

function authenticateToken(req, res, next) {
  // Get the token from the 'Authorization' header
  const authHeader = req.headers["authorization"];
  const token = authHeader && authHeader.split(" ")[1]; // Expected format: 'Bearer token'

  if (!token) {
    return res.status(401).json({ error: "Unauthorized" });
  }

  // Verify the token
  jwt.verify(token, secretKey, (err, decoded) => {
    if (err) {
      return res.status(403).json({ error: "Token is invalid" });
    }

    // Attach user info to the request object for access in the next middleware
    req.user = decoded;

    // Proceed to the next middleware or endpoint handler
    next();
  });
}

module.exports = { authenticateToken };
```
<br /> 

```js {*}{maxHeight:'35vh'}
const express = require("express");
const http = require("http");
const app = express();
const cors = require("cors");
const jwt = require("jsonwebtoken");
const WebSocket = require("ws");

const { authenticateToken } = require("./middleware");

app.use(cors()); // Make this server accessible by other applicationss
app.use(express.json()); // Allow server to parse json contents from the body

const secretKey = "hard-to-guess-characters"; // normally, this is saved in a .env file

let sensors = [
  {
    user: "paul",
    enabled: false,
    value: 100,
  },
];

// Imagine this is coming from database
const users = [
  {
    username: "paul",
    password: "123", // normally, this is encrypted (s1@#2aIO27@29mcx23)
  },
];

// Login endpoint to generate JWT
app.post("/login", (req, res) => {
  const loginInput = req.body;
  const user = users.find(
    (u) =>
      u.username === loginInput.username && u.password === loginInput.password
  );

  if (user) {
    const token = jwt.sign({ username: user.username }, secretKey, {
      expiresIn: "1d",
    });
    res.json({ token });
  } else {
    res.status(401).json({ error: "Invalid credentials" });
  }
});

// Retrieve all sensors (if user is logged in)
app.get("/api/sensors", authenticateToken, (request, response) => {
  response.json(sensors);
});

// Create HTTP server
const httpServer = http.createServer(app);

// Create a WebSocket 4000
const websocketServer = new WebSocket.Server({ server: httpServer });

websocketServer.on("connection", (ws, req) => {
  // Extract token from query parameter
  const token = new URL(req.url, `http://${req.headers.host}`).searchParams.get(
    "token"
  );

  jwt.verify(token, secretKey, (err, decoded) => {
    if (err) {
      ws.send(
        JSON.stringify({ message: "Unauthorized" })
      );
      ws.close();
    } else {
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
            sensors = sensors.filter(
              (sensor) => sensor.user !== userToBeDeleted
            );
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
    }
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

console.log("WebSocket server running on ws://localhost:3000");

// Start server on port 3000
httpServer.listen(3000, () => {
  console.log(`Http server running on http://localhost:3000`);
});
```
<br />

<AuthenticationDemo />


<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>
