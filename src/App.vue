<template>
  <div class="app">
    <div ref="remIndicator" class="rem-indicator"></div>
    <router-view />
  </div>
</template>

<script setup>
import { ref, onMounted, provide } from 'vue'

const remIndicator = ref(null)

const remToPixel = ref(16)

const convertRemToPixels = (rem) => {
  return rem * remToPixel.value
}

const convertPixelsToRem = (pixels) => {
  return pixels / remToPixel.value
}

onMounted(() => {
  if (remIndicator.value) {
    const computedStyle = window.getComputedStyle(remIndicator.value)
    remToPixel.value = parseFloat(computedStyle.width)
    const resizeObserver = new ResizeObserver(() => {
      const computedStyle = window.getComputedStyle(remIndicator.value)
      remToPixel.value = parseFloat(computedStyle.width)
    })

    resizeObserver.observe(remIndicator.value)
  }
})

provide('convertRemToPixels', convertRemToPixels)
provide('convertPixelsToRem', convertPixelsToRem)
provide('remToPixel', remToPixel)
</script>

<style>
.rem-indicator {
  position: fixed;
  width: 1rem;
  height: 1rem;
  top: -9999px;
  left: -9999px;
  visibility: hidden;
  pointer-events: none;
}
</style>
