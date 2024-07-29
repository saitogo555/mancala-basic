<template>
  <div class="stone" :style="cssVars">
    
  </div>
</template>

<style lang="scss" scoped>
.stone {
  position: relative;
  width: 29.3333%;
  height: 0;
  margin: 2%;
  padding-bottom: 29.3333%;
  border-radius: 50%;
  background-color: var(--color);
  box-shadow: 0.1vmin 0.1vmin 0.1vmin #00000080,
              inset 0.3vmin 0.3vmin 0.4vmin #ffffff80,
              inset -0.4vmin -0.4vmin 0.4vmin #44444480;
}
</style>

<script setup lang="ts">
import { reactive } from 'vue';

const props = defineProps({
  color: {
    type: String
  }
})

const cssVars = reactive({
  "--color": props.color
})
</script>