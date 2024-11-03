<template>
  <div class="flex flex-col gap-8 p-4">
    <div class="flex-grow flex items-center flex-col justify-start gap-4">
      <div class="w-full grid grid-cols-2 gap-4 items-center text-2xl">
        <p class="">Boxing Time (s):</p>
        <NumberInput
          key="Boxing Timer"
          :step="30"
          :min="30"
          :max="600"
          v-model="boxingTimer"
          placeholder="in seconds"
        />
      </div>

      <div class="quickSettingsContainer">
        <div
          class="quickSettings"
          :class="checkSetting(boxingTimer, item)"
          @click="quickSet('boxing', item)"
          v-for="item in [60, 90, 120, 180, 240, 300]"
          :key="item"
        >
          {{ item }}
        </div>
      </div>

      <div class="w-full grid grid-cols-2 gap-4 items-center text-2xl">
        <p class="">Rest Time (s):</p>
        <NumberInput
          key="Rest Timer"
          :step="15"
          :min="15"
          :max="120"
          v-model="restTimer"
          placeholder="in seconds"
        />
      </div>
      <div class="quickSettingsContainer">
        <div
          class="quickSettings"
          :class="checkSetting(restTimer, item)"
          @click="quickSet('rest', item)"
          v-for="item in [15, 30, 45, 60]"
          :key="item"
        >
          {{ item }}
        </div>
      </div>
    </div>
    <div class="grid gap-8 grid-cols-2">
      <ActionButton
        key="Start Button"
        @click="$emit('startCountDown', { boxingTimer, restTimer })"
        bg-color="bg-green-700"
        >Start</ActionButton
      >
      <ActionButton key="Reset Button" @click="reset" bg-color="bg-gray-700"
        >Reset</ActionButton
      >
    </div>
  </div>
</template>

<script setup>
import NumberInput from "@/components/NumberInput.vue";
import ActionButton from "@/components/Button.vue";
import { ref } from "vue";
const boxingTimer = ref(0);
const restTimer = ref(0);

const emits = defineEmits(["startCountDown", "reset"]);

function quickSet(action, duration) {
  if (action === "boxing") {
    boxingTimer.value = duration;
  }
  if (action === "rest") {
    restTimer.value = duration;
  }
}

function checkSetting(a, b) {
  if (a == b) {
    return "bg-blue-900 text-white ";
  }
  return "bg-blue-400 text-black-50";
}

function reset() {
  boxingTimer.value = 0;
  restTimer.value = 0;
  emits("reset");
}
</script>

<style lang="scss" scoped></style>
