<template>
  <div :class="styleClass">
    <slot></slot>
  </div>
</template>

<script setup lang='ts'>
const props = defineProps({
  width: {
    type: String,
    default: '1fr'
  },
  gap: {
    type: Number,
    default: 4
  }
})

const styleClass = `grid grid-cols-2 mb-4 gap-${props.gap} !grid-cols-[${props.width}_1fr]`
</script>

