<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import { database } from "./firebase";
import { ref as firebaseRef, onValue, update } from "firebase/database";

const data = ref(null);
const sensors = ref({});
const activeUser = ref(null); // To track which user has microphone access

const intervalId = ref(null);
const unsubscribe = ref(null);

// Function to fetch data
const fetchData = async () => {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    data.value = await response.json();
  } catch (error) {
    console.error("Failed to fetch data", error);
  }
};

// Set an interval to fetch data every X seconds
onMounted(() => {
  unsubscribe.value = onValue(firebaseRef(database, "sensors"), (snapshot) => {
    sensors.value = snapshot.val();
    Object.keys(snapshot.val()).forEach((user) => {
      if (snapshot.val()[user].enabled) {
        activeUser.value = user; // Set the active user
      }
    });
  });
});

// Clean up the Firebase listener on component unmount
onUnmounted(() => {
  if (unsubscribe.value) {
    unsubscribe.value();
  }
});

// Handle microphone access switch
const toggleMicAccess = (username) => {
  // Disable all other users, then enable selected user
  const updates = {};
  Object.keys(sensors.value).forEach((user) => {
    updates[`sensors/${user}/enabled`] = user === username;
  });

  console.log(updates);
  update(firebaseRef(database), updates);
  activeUser.value = username;
};
</script>

<template>
  <div>
    <h3 class="mb-10">Mocked mic sensor</h3>

    <div class="flex">
      <div class="space-y-4 flex-1">
        <span class="mb-10">Sensors: </span>
        <div
          v-for="(sensor, username) in sensors"
          :key="username"
          class="flex items-center space-x-2"
        >
          <span :class="username === activeUser ? 'text-orange-500' : ''">{{
            `${username} ${username === activeUser ? "(speaker 🗣️)" : ""}`
          }}</span>

          <button
            v-if="!sensor.enabled"
            @click="toggleMicAccess(username)"
            class="border border-orange-600 text-orange-600 px-2 py-1 text-sm rounded"
          >
            Give 🎤
          </button>
        </div>
      </div>

      <div class="flex-1" v-if="activeUser">
        <h3>Active sensor: {{ activeUser }}</h3>
        <div class="sound-bar">
          <div
            class="bar"
            :style="{ height: sensors[activeUser].value + '%' }"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sound-bar {
  width: 50px;
  height: 200px;
  background-color: lightgray;
  margin-top: 10px;
  position: relative;
}

.bar {
  background-color: green;
  width: 100%;
  position: absolute;
  bottom: 0;
  height: 0%;
  transition: height 0.5s ease; /* Animation */
}
</style>
