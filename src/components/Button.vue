<template>
  <div class="rounded-md overflow-hidden">
    <div class="btn active" :class="bgColor">
      <slot></slot>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  bgColor: {
    type: String,
    default: "",
  },
});
</script>

<style lang="scss" scoped>
@property --angle {
  syntax: "<angle>";
  inherits: false;
  initial-value: 0deg;
}
.btn{
    --angle: 0deg;
}
.btn {
  @apply rounded-lg text-center w-full px-6 py-4 lg:px-12 lg:py-8 cursor-pointer text-white uppercase 
  text-xl md:text-3xl font-black tracking-wide hover:opacity-70 border-8;
  // border-image: linear-gradient(to right, #0066ff, #ff32de) 1 1 1 1;
  // background-image: conic-gradient(from var(--angle), red, blue, red);
  border-image: conic-gradient(from var(--angle), red, blue, red) 1;
  &.active {
    animation: 5s colorspin linear infinite;
  }
}

@keyframes colorspin {
  from {
    --angle: 0deg;
  }

  to {
    --angle: 360deg;
  }
}
</style>
