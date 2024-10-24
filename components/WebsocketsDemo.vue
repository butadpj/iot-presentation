<script setup>
import { onUnmounted } from "vue";
import { ref } from "vue";

const data = ref(null);
let websocket = null;

const startConnection = async () => {
  if (!websocket) {
    websocket = new WebSocket("ws://localhost:4000");

    websocket.onopen = () => {
      console.log("Connected to WebSocket");
      websocket.send(JSON.stringify({ action: "GET_ALL_SENSORS" }));
    };

    websocket.onmessage = (event) => {
      const { action, data: sensors, message } = JSON.parse(event.data);
      if (message) console.log(`From websocket server: ${message}`);

      if (action === "GET_ALL_SENSORS") {
        data.value = sensors;
      }
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
    <div class="space-y-2">
      <div>
        <button
          :class="`px-2 py-1 rounded  ${
            !websocket ? 'bg-orange-500 hover:bg-orange-600' : 'bg-green-500 hover:bg-green-600'
          }
            `"
          @click="startConnection()"
        >
          {{ !websocket ? "Start the connection" : "Connected" }}
        </button>
        <pre>{{ data }}</pre>
      </div>
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
