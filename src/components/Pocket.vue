<template>
  <div class="pocket">
    <div class="pocket__label">{{ props.num }}</div>
    <slot></slot>
  </div>
</template>

<style lang="scss" scoped>
.pocket {
  display: flex;
  align-content: flex-start;
  flex-wrap: wrap;
  width: 90%;
  height: 95%;
  margin: 0 auto;
  padding: 5%;
  border: solid 1px grey;
  border-radius: 20px;
  box-shadow: inset 3px 3px 1px #444444,
              inset 4px 4px 3px #888888;
  overflow: auto;
  cursor: pointer;

  &__label {
    width: 100%;
    text-align: center;
  }

  &:nth-of-type(n+1):nth-of-type(-n+6) {
    margin-top: auto;
  }

  &:nth-of-type(1) {
    grid-column: 2 / 3;
    grid-row: 2 / 3;
  }

  &:nth-of-type(2) {
    grid-column: 3 / 4;
    grid-row: 2 / 3;
  }

  &:nth-of-type(3) {
    grid-column: 4 / 5;
    grid-row: 2 / 3;
  }

  &:nth-of-type(4) {
    grid-column: 5 / 6;
    grid-row: 2 / 3;
  }

  &:nth-of-type(5) {
    grid-column: 6 / 7;
    grid-row: 2 / 3;
  }

  &:nth-of-type(6) {
    grid-column: 7 / 8;
    grid-row: 2 / 3;
  }

  &:nth-of-type(7) {
    grid-column: 8 / 9;
    grid-row: 1 / 3;
    height: 100%;
  }

  &:nth-of-type(8) {
    grid-column: 7 / 8;
    grid-row: 1 / 2;
  }

  &:nth-of-type(9) {
    grid-column: 6 / 7;
    grid-row: 1 / 2;
  }

  &:nth-of-type(10) {
    grid-column: 5 / 6;
    grid-row: 1 / 2;
  }

  &:nth-of-type(11) {
    grid-column: 4 / 5;
    grid-row: 1 / 2;
  }

  &:nth-of-type(12) {
    grid-column: 3 / 4;
    grid-row: 1 / 2;
  }

  &:nth-of-type(13) {
    grid-column: 2 / 3;
    grid-row: 1 / 2;
  }

  &:nth-of-type(14) {
    grid-column: 1 / 2;
    grid-row: 1 / 3;
    height: 100%;
  }
}
</style>

<script setup lang="ts">
const props = defineProps({
  num: {
    type: Number,
  }
})
</script>