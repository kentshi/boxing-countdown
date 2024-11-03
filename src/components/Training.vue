<template>
  <div class="h-full flex flex-col gap-12 p-4 justify-between">
    <div class="py-4">
      <p class="text-center text-4xl uppercase text-blue-900 font-bold">
        Round {{ round }}
      </p>
      <p
        v-text="timerDisplay"
        :class="[state == 'rest' ? 'text-green-500' : 'text-white']"
        class="w-full leading-none text-center text-9xl"
      ></p>
    </div>
    <icon-boxing-gloves
      class="boxingglove mx-auto max-w-full h-48 text-red-900"
      v-show="state == 'boxing'"
    ></icon-boxing-gloves>
    <icon-hour-glass
      class="hourglass mx-auto max-w-full h-48 text-green-900"
      style="animation-duration: 3s"
      v-show="state == 'rest'"
    ></icon-hour-glass>
    <icon-hour-glass
      class="mx-auto max-w-full h-48 text-gray-700"
      v-show="state == 'paused'"
    ></icon-hour-glass>

    <div class="grid gap-8 grid-cols-2">
      <ActionButton
        key="Resume"
        v-if="state == 'paused'"
        @click="$emit('resumeCountDown')"
        bg-color="bg-green-700"
        >Resume</ActionButton
      >
      <ActionButton
        key="Pause"
        v-else
        @click="$emit('pauseCountDown')"
        bg-color="bg-orange-700"
        >Pause</ActionButton
      >
      <ActionButton
        key="Stop"
        @click="$emit('stopCountDown')"
        bg-color="bg-red-700"
        >Stop</ActionButton
      >
    </div>
  </div>
</template>

<script setup>
import IconBoxingGloves from "@/components/icons/IconBoxingGloves.vue";
import IconHourGlass from "@/components/icons/IconHourGlass.vue";
import ActionButton from "@/components/Button.vue";
import { computed } from "vue";

const emits = defineEmits([
  "pauseCountDown",
  "stopCountDown",
  "resumeCountDown",
]);

const props = defineProps({
  state: {
    type: String,
    default: "",
  },
  timer: {
    type: Number,
    default: 0,
  },
  round: {
    type: Number,
    default: 0,
  },
});

const timerDisplay = computed(() => {
  const min = parseInt(props.timer / 60);
  const sec = props.timer - min * 60;
  return String(min).padStart(2, "0") + ":" + String(sec).padStart(2, "0");
});
</script>

<style lang="scss" scoped></style>
