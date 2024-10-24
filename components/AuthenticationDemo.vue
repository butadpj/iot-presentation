<script setup>
import { onUnmounted } from "vue";
import { ref } from "vue";

const data = ref(null);
const realtimeData = ref(null);
let websocket = null;
let token = null;

const login = async () => {
  const response = await fetch("http://localhost:3000/login", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      username: "paul",
      password: "123",
    }),
  });
  const data = await response.json();

  if (!data.token) {
    alert(data.error);
  } else {
    token = data.token;
    alert("Login success! You can now connect to Websocket server");
    // localStorage.setItem("token", token);
  }
};

const fetchData = async () => {
  const response = await fetch("http://localhost:3000/api/sensors", {
    method: "GET",
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });
  const sensors = await response.json();

  if (sensors.error) {
    alert("Please login first");
  } else {
    data.value = sensors;
  }
};

const startConnection = async () => {
  if (!websocket) {
    console.log("TOKEN HERE: ", token);
    websocket = new WebSocket(`ws://localhost:3000`);

    websocket.onopen = () => {
      console.log("Connected to WebSocket");
      websocket.send(JSON.stringify({ action: "GET_ALL_SENSORS" }));
    };

    websocket.onmessage = (event) => {
      const { action, data, message } = JSON.parse(event.data);
      if (message === "Unauthorized") {
        alert("Please login first");
      } else {
        console.log(`From websocket server: ${message}`);
      }

      if (action === "GET_ALL_SENSORS") {
        realtimeData.value = data;
      }
    };

    websocket.onclose = (event) => {
      websocket = null;
    };
  }
};

const addJohn = async () => {
  websocket.send(
    JSON.stringify({
      action: "ADD_SENSOR",
      payload: { user: "john", enabled: false, value: 0 },
    })
  );
};

const updateJohn = async () => {
  websocket.send(
    JSON.stringify({
      action: "UPDATE_SENSOR",
      payload: { user: "john", newData: { enabled: true, value: 9999 } },
    })
  );
};

const deleteJohn = async () => {
  websocket.send(
    JSON.stringify({
      action: "DELETE_SENSOR",
      payload: { user: "john" },
    })
  );
};

onUnmounted(() => {
  websocket.close();
});
</script>

<template>
  <div class="absolute top-1/2 right-[15%] -translate-y-[50%] space-y-8">
    <div class="flex flex-col items-start space-y-2">
      <button
        :class="`px-2 py-1 rounded bg-orange-500 hover:bg-orange-600`"
        @click="login()"
      >
        Login
      </button>
      <button
        class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
        @click="fetchData()"
      >
        Fetch once
      </button>
      <pre>{{ data }}</pre>
      <button
        :class="`px-2 py-1 rounded  ${
          !websocket
            ? 'bg-orange-500 hover:bg-orange-600'
            : 'bg-green-500 hover:bg-green-600'
        }
            `"
        @click="startConnection()"
      >
        {{ !websocket ? "Start the connection" : "Connected" }}
      </button>
      <pre>{{ realtimeData }}</pre>
    </div>

    <div class="space-x-2">
      <button
        class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
        @click="addJohn()"
      >
        Add John
      </button>

      <button
        class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
        @click="updateJohn()"
      >
        Update John
      </button>

      <button
        class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
        @click="deleteJohn()"
      >
        Delete John
      </button>
    </div>
  </div>
</template>
