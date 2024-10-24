<script setup>
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from "chart.js";
import { onMounted } from "vue";
import { ref } from "vue";
import { Pie } from "vue-chartjs";
import { onValue, ref as firebaseRef } from "firebase/database";
import { database } from "./firebase";

const choices = ref([]);
const responses = ref(null);

onMounted(() => {
  onValue(firebaseRef(database, "familiarity"), (snapshot) => {
    const choicesData = snapshot.val().choices;
    const responsesData = snapshot.val().responses;

    // Saving firebase data locally (choices ref)
    choicesData.forEach((choice) => {
      if (!choices.value.includes(choice)) choices.value.push(choice);
    });

    // Count each responses (initial value is 0)
    const countedResponses = choices.value.reduce(
      (final, choice) => ({ ...final, [choice]: 0 }),
      {}
    );

    Object.keys(responsesData).forEach((response) => {
      if (choices.value.includes(responsesData[response])) {
        countedResponses[responsesData[response]]++;
      }
    });

    console.log("COUNTED RESPONSES: ", countedResponses);
    responses.value = countedResponses;
  });
});

const getData = () => {
  return {
    labels: choices.value,
    datasets: [
      {
        backgroundColor: ["#E46651", "#41B883", "#00D8FF"],
        data: choices.value.map((choice) => responses.value[choice]),
      },
    ],
  };
};

ChartJS.register(ArcElement, Tooltip, Legend);
</script>

<template>
  <main class="flex items-center justify-center">
    <div class="mt-10" v-if="responses && choices.length">
      <Pie
        :data="getData()"
        :options="{
          responsive: false,
          maintainAspectRatio: true,
        }"
      />
    </div>
  </main>
</template>

<style></style>
