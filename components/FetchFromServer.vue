<script setup>
import { ref } from "vue";

const data = ref(null);

const fetchData = async () => {
  const response = await fetch("http://localhost:3000/api/sensors");
  const sensors = await response.json();

  data.value = sensors;
};
</script>

<template>
  <div class="absolute top-1/2 right-[15%] -translate-y-[50%]">
    <button
      class="bg-orange-500 px-2 py-1 rounded hover:bg-orange-600"
      @click="fetchData()"
    >
      Fetch data from server
    </button>
    <!-- <div v-if="data?.length" class="mt-10">
      <div
        v-for="sensor in data"
        :key="sensor.user"
        class="flex items-center space-x-2"
      >
        <span class="text-orange-500">{{ sensor.user }}</span>

        <button
          v-if="!sensor.enabled"
          class="border border-orange-600 text-orange-600 px-2 py-1 text-sm rounded hover:opacity-50"
        >
          Give 🎤
        </button>
      </div>
    </div> -->
    <pre>{{ data }}</pre>
  </div>
</template>
