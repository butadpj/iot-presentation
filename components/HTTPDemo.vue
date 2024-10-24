<script setup>
import { onUnmounted } from "vue";
import { ref } from "vue";

const data = ref(null);
const continuousData = ref(null);
const intervalId = ref(null);

const fetchData = async () => {
  const response = await fetch("http://localhost:3000/api/sensors");
  const sensors = await response.json();

  data.value = sensors;
};

const longPollData = () => {
  intervalId.value = setInterval(async () => {
    const response = await fetch("http://localhost:3000/api/sensors");
    const sensors = await response.json();

    continuousData.value = sensors;
  }, 100);
};

const addJohn = async () => {
  const response = await fetch("http://localhost:3000/api/sensors", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      user: "john",
      enabled: false,
      value: 0,
    }),
  });

  if (response.ok) {
    console.log("Added new user: john");
  }
};

const updateJohn = async () => {
  const response = await fetch("http://localhost:3000/api/sensors/john", {
    method: "PUT",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      enabled: true,
      value: 9999,
    }),
  });

  if (response.ok) {
    console.log("Added new user: john");
  }
};

const deleteJohn = async () => {
  const response = await fetch("http://localhost:3000/api/sensors/john", {
    method: "DELETE",
  });

  if (response.ok) {
    console.log("Deleted user: john");
  }
};

onUnmounted(() => {
  clearInterval(intervalId.value);
});
</script>

<template>
  <div class="absolute top-1/2 right-[15%] -translate-y-[50%] space-y-8">
    <div class="space-y-2">
      <div>
        <button
          class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
          @click="fetchData()"
        >
          Fetch once
        </button>
        <pre>{{ data }}</pre>
      </div>
      <div>
        <button
          class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
          @click="longPollData()"
        >
          Fetch continuously (long polling)
        </button>
        <pre>{{ continuousData }}</pre>
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
