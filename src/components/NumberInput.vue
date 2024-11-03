<template>
  <input
    class="w-full text-center text-3xl py-2 rounded-md font-bold"
    :step="step"
    type="number"
    :min="min"
    :max="max"
    onkeypress="return (event.charCode !=8 && event.charCode ==0 || (event.charCode >= 48 && event.charCode <= 57))"
    :placeholder="placeholder"
    :value="modelValue"
    @input="emit('update:modelValue', parseInt($event.target.value))"
  />
</template>

<script setup>
const props = defineProps({
  min: { type: Number, default: 0 },
  max: { type: Number, default: 0 },
  step: { type: Number, default: 0 },
  placeholder: { type: String, default: "" },
  modelValue: { type: Number, default: 0 },
});

const emit = defineEmits(["update:modelValue"]);
</script>

<style lang="scss" scoped></style>
