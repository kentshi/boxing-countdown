<script setup>
import IconBoxingGloves from "./components/icons/IconBoxingGloves.vue";
import IconHourGlass from "./components/icons/IconHourGlass.vue";
import { computed, ref, watch } from "vue";
const timer = ref(0);
const state = ref("stopped");
const boxingTimer = ref(0);
const restTimer = ref(0);
const timeout = ref(null);
const stateBeforePaused = ref("");

const ding = new Audio('/ding.wav');
const notify = new Audio('/default.wav');

const timerDisplay = computed(() => {
  const min = parseInt(timer.value / 60);
  const sec = timer.value - (min * 60);
  return String(min).padStart(2, '0') + ":" + String(sec).padStart(2, '0');
});
function reset(){
  changeState('stopped');
  boxingTimer.value = 0;
  restTimer.value = 0;
}
function changeState(newState) {
  state.value = newState;
}
function rest() {
  changeState("rest");
  timer.value = restTimer.value;
}
function boxing() {
  changeState("boxing");
  timer.value = boxingTimer.value;
}
function countdown()
{
  if (timer.value > 0) {
    if(timer.value <= 5){
      playSound('notify');
    }
    timer.value--;
  } else {
    if (state.value === "rest") {
      playSound('ding');
      boxing();
    } else {
      rest();
    }
  }
}
function startCountDown() {
  if(parseInt(boxingTimer.value) <= 0){
    alert('You must set the boxing timer');
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
  changeState("paused");
  stateBeforePaused.value = state.value;
}
function stopCountDown() {
  clearInterval(timeout.value);
  timer.value = 0;
  changeState("stopped");
}
function quickSet(action, duration) {
  if (action === "boxing") {
    boxingTimer.value = duration;
  }
  if (action === "rest") {
    restTimer.value = duration;
  }
}
function playSound(soundname)
{
  switch (soundname) {
    case 'ding':
      ding.play();
      break;
    case 'notify':
      notify.play();
      break;
    default:
      break;
  }
}
</script>

<template>
  <div class="bg-gray-500 h-full w-full">
    <div
      class="h-full flex flex-col justify-between items-center gap-8"
      v-if="state == 'stopped'"
    >
      <div class="w-full lg:w-3/5 flex-grow flex items-center flex-col justify-start gap-4">
        <div class="w-full block text-3xl lg:text-7xl my-4 px-4">
          <p class="mb-4 lg:mb-0">Boxing Time (s):</p>
          <input
            class="w-full text-center py-4 rounded-lg  "
            step="30"
            type="number"
            min="30"
            max="600"
            onkeypress="return (event.charCode !=8 && event.charCode ==0 || (event.charCode >= 48 && event.charCode <= 57))"
            v-model.number="boxingTimer"
            placeholder="in seconds"
          />
        </div>
        <div class="flex justify-evenly items-center w-full flex-wrap gap-4">
          <div
            class="quickSettings"
            @click="quickSet('boxing', item)"
            v-for="item in [60, 90, 120, 180, 240, 300]"
            :key="item"
          >
            {{ item }}
          </div>
        </div>
        <div class="w-full block text-3xl lg:text-7xl my-4 px-4">
          <p class="mb-4 lg:mb-0">Rest Time (s):</p>
          <input
            class="w-full text-center py-4 rounded-lg"
            step="15"
            type="number"
            min="15"
            max="120"
            onkeypress="return (event.charCode !=8 && event.charCode ==0 || (event.charCode >= 48 && event.charCode <= 57))"
            v-model.number="restTimer"
            placeholder="in seconds"
          />
        </div>
        <div class="flex justify-evenly items-center w-full flex-wrap gap-4">
          <div
            class="quickSettings"
            @click="quickSet('rest', item)"
            v-for="item in [15, 30, 45, 60]"
            :key="item"
          >
            {{ item }}
          </div>
        </div>
      </div>

      <div class="grid gap-8 grid-cols-2 mb-4">
        <div class="btn bg-green-700" @click="startCountDown">Start</div>
        <div class="btn bg-gray-600" @click="reset">Reset</div>
      </div>
    </div>
    <!-- start boxing -->
    <div
      v-else
      class="h-full flex flex-col justify-center items-center py-8"
      :class="{
        'bg-green-700': state == 'rest',
        'bg-red-400': state == 'boxing',
        'bg-orange-700': state == 'paused',
      }"
    >
      <div class="flex-grow">
        <p
          v-text="timerDisplay"
          :class="[state == 'rest' ? 'text-green-500' : 'text-white']"
          class="w-full leading-tight text-center mb-8 text-9xl"
        ></p>
        <icon-boxing-gloves
          class="boxingglove mx-auto max-w-full w-2/3 text-red-900"
          v-if="state == 'boxing'"
        ></icon-boxing-gloves>
        <icon-hour-glass
          class="hourglass mx-auto max-w-full w-1/3 text-green-900"
          style="animation-duration: 3s;"
          v-if="state == 'rest'"
        ></icon-hour-glass>
      </div>

      <div class="grid gap-8 grid-cols-2">
        <div
          v-if="state == 'paused'"
          class="btn bg-green-700"
          @click="resumeCountDown"
        >
          Resume
        </div>
        <div v-else class="btn active bg-orange-700" @click="pauseCountDown">
          Pause
        </div>
        <div class="btn active bg-red-700" @click="stopCountDown">Stop</div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@property --angle{
  syntax: "<angle>";
    initial-value: 0deg;
    inherits: false;
}
.btn {
  @apply rounded-lg text-center w-full px-6 py-4 lg:px-12 lg:py-8 cursor-pointer text-white uppercase text-3xl font-black tracking-wide hover:opacity-70 border-8;
  // border-image: linear-gradient(to right, #0066ff, #ff32de) 1 1 1 1;
  // background-image: conic-gradient(from var(--angle), red, blue, red);
  border-image: conic-gradient(from var(--angle), red, blue, red) 1;
  &.active{
    animation: 5s colorspin linear infinite;
  }
}

@keyframes colorspin{
  from {
    --angle: 0deg;
  }

  to{
    --angle: 360deg;
  }
}

.quickSettings {
  @apply rounded-md text-center px-6 py-3 cursor-pointer bg-blue-900 text-white font-bold text-2xl hover:opacity-60;
}

.boxingglove{
  @apply animate-bounce;
}

.hourglass{
  @apply animate-spin;
}
</style>
