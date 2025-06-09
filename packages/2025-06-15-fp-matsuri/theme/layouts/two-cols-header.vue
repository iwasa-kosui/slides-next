<!--
  Usage:
```md
---
layout: two-cols-header
---
This spans both
::left::
# Left
This shows on the left
::right::
# Right
This shows on the right
::bottom::
This shows at the bottom, aligned to the end (bottom) of the grid
```
-->

<script setup lang="ts">
const props = defineProps({
  layoutClass: {
    type: String,
  },
  leftClass: {
    type: String,
  },
  rightClass: {
    type: String,
  },
})
</script>

<template>
  <div class="slidev-layout two-cols-header w-full h-full grid grid-rows-[auto_auto_1fr] grid-cols-2 gap-x-8 gap-y-0" :class="layoutClass">
    <div class="col-header">
      <slot />
    </div>
    <div class="col-left mb-2" :class="props.leftClass">
      <slot name="left" />
    </div>
    <div class="col-right mb-2" :class="props.rightClass">
      <slot name="right" />
    </div>
    <div class="col-bottom">
      <slot name="bottom" />
    </div>
  </div>
</template>

<style>
.two-cols-header {
  .col-header {
    grid-area: 1 / 1 / 2 / 3;
  }
  .col-left {
    grid-area: 2 / 1 / 3 / 2;
  }
  .col-right {
    grid-area: 2 / 2 / 3 / 3;
  }
  .col-bottom {
    grid-area: 3 / 1 / 3 / 3;
  }
  &.two-cols-card {
    .col-left,
    .col-right {
      @apply bg-amber-100 bg-opacity-50 p-4 rounded-lg;
    }
  }
}

.dark .two-cols-header {
  &.two-cols-card {
    @apply text-white;

    .col-left,
    .col-right {
      @apply bg-zinc-800 text-white;
    }
  }
}
</style>
