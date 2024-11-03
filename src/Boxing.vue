<script setup>
import Settings from "@/components/Settings.vue";
import Training from "@/components/Training.vue";
import { ref } from "vue";

const timer = ref(0);
const round = ref(0);
const state = ref("stopped");
const boxingTimer = ref(0);
const restTimer = ref(0);
const timeout = ref(null);
const stateBeforePaused = ref("");

const ding = new Audio("/ding.wav");
const notify = new Audio("/default.wav");

function changeState(newState = "") {
  state.value = newState;
}
function reset() {
  boxingTimer.value = 0;
  restTimer.value = 0;
  stopCountDown();
}
function rest() {
  changeState("rest");
  timer.value = restTimer.value;
}
function boxing() {
  changeState("boxing");
  timer.value = boxingTimer.value;
}
function countdown() {
  if (timer.value > 0) {
    if (timer.value <= 5) {
      playSound("notify");
    }
    timer.value--;
  } else {
    if (state.value === "rest") {
      playSound("ding");
      boxing();
      round.value++;
    } else {
      rest();
    }
  }
}
function startCountDown(argument) {
  boxingTimer.value = argument.boxingTimer;
  restTimer.value = argument.restTimer;
  if (parseInt(boxingTimer.value) <= 0) {
    alert("You must set the boxing timer");
    return false;
  }
  rest();
  clearInterval(timeout.value);
  timeout.value = setInterval(() => {
    countdown();
  }, 1000);
}
function resumeCountDown() {
  changeState(stateBeforePaused.value);
  timeout.value = setInterval(() => {
    countdown();
  }, 1000);
}
function pauseCountDown() {
  clearInterval(timeout.value);
  stateBeforePaused.value = state.value;
  changeState("paused");
}
function stopCountDown() {
  clearInterval(timeout.value);
  timer.value = 0;
  round.value = 0;
  stateBeforePaused.value = "";
  changeState("stopped");
}
function playSound(soundname) {
  switch (soundname) {
    case "ding":
      ding.play();
      break;
    case "notify":
      notify.play();
      break;
    default:
      break;
  }
}
</script>

<template>
  <div
    class="transition-colors h-full"
    :class="{
      'bg-green-700': state == 'rest',
      'bg-red-400': state == 'boxing',
      'bg-orange-700': state == 'paused',
      'bg-gray-500': state == 'stopped',
    }"
  >
    <div class="max-w-screen-lg mx-auto h-full sm:h-auto">
      <h1
        v-show="state === 'stopped'"
        class="text-black text-4xl text-center py-4 font-bold"
      >
        Count Down Timer (v1.0)
      </h1>
      <Settings
        v-on:start-count-down="startCountDown"
        v-on:reset="reset"
        v-show="state == 'stopped'"
      ></Settings>
      <Training
        v-show="state != 'stopped'"
        :state="state"
        :timer="timer"
        :round="round"
        v-on:stop-count-down="stopCountDown"
        v-on:pause-count-down="pauseCountDown"
        v-on:resume-count-down="resumeCountDown"
      ></Training>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
