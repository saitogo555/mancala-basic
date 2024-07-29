<template>
  <div class="board">
    <slot></slot>
  </div>
</template>

<style lang="scss" scoped>
.board {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  width: 100vmin;
  height: 60vmin;
  margin: 0 auto;
  padding: 1%;
  border: solid 1px grey;
  border-bottom-width: 6px;
  border-radius: 20px;
  background-image: url("../assets/bg.jpg");
  background-position: center;
  background-size: cover;
  box-shadow: 0px 4px 6px #44444480;
}
</style>