<template>
  <div class="game">
    <div v-if="isFinishid" class="result">
      <div class="result__text">{{ turn }}Pの勝利</div>
      <button class="retry" @click="retry">もう一度遊ぶ</button>
    </div>
    <div class="links">
      <a class="links__btn links__btn--rule" :href="RulePDF" target="_blank">
        ルールはこちらから
      </a>
      <a class="links__btn links__btn--ref" href="https://recreation.or.jp/activities/genki_up/mancala/#basic" target="_blank">
        引用元: 日本レクリエーション協会
      </a>
    </div>
    <Board>
      <Pocket 
        v-for="(pocket, pocketIndex) in pockets"
        :class="{
          'selected': selectedIndex === pocketIndex,
          'selectable': (turn - 1) * 7 <= pocketIndex && pocketIndex <= turn * 7 - 2 && pocket.length && !isProcessing
        }"
        :num="pocketIndex + 1"
        @click="selection(pocketIndex)"
      >
        <TransitionGroup>
          <Stone v-for="(stone, stoneIndex) in pocket" :key="pocketIndex + '-' + stoneIndex" :color="stone"></Stone>
        </TransitionGroup>
      </Pocket>
    </Board>
    <div class="text">{{ text }}</div>
    <button class="enter-btn" :disabled="selectedIndex === -1 || !pockets[selectedIndex].length || isProcessing" @click="decision">決定</button>
  </div>
</template>

<style lang="scss" scoped>
.game {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

.result {
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.75);
  z-index: 999;

  &__text {
    color: #eeeeee;
    font-size: 10vw;
    font-weight: bold;
  }
}

.retry {
  display: table;
  width: 50vmin;
  height: 5vmin;
  color: #eeeeee;
  font-size: 2vmin;
  font-weight: bold;
  padding: 0 2vmin;
  border: none;
  border-radius: 1vmin;
  background-color: rgb(17, 143, 0);
  cursor: pointer;
}

.text {
  width: 100vmin;
  height: 5vmin;
  line-height: 5vmin;
  font-size: 3vmin;
  font-weight: bold;
  text-align: center;
  margin-top: 2vmin;
}

.enter-btn {
  display: table;
  width: 50vmin;
  height: 5vmin;
  color: #eeeeee;
  font-size: 2vmin;
  font-weight: bold;
  margin-top: 2vmin;
  padding: 0 2vmin;
  border: none;
  border-radius: 1vmin;
  background-color: rgb(0, 102, 255);
  transition: all 0.2s;
  cursor: pointer;

  &:disabled {
    background-color: darkgray;
  }
}

.links {
  position: fixed;
  top: 1rem;
  right: 1rem;
  display: flex;
  gap: 1rem;
}

.links__btn {
  font-size: 1rem;
  font-weight: bold;
  text-decoration: none;
  padding: 0.5rem 1rem;
  border-radius: 1vmin;
  transition: all 0.2s;

  &:hover {
    filter: brightness(0.9);
  }

  &--rule {
    color: #7fb71c;
    border: solid 1px #7fb71c;
    background-color: #ffffff;
  }

  &--ref {
    color: #ffffff;
    background-color: #7fb71c;
  }
}

.selectable, .selected {
  position: relative;

  &::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: inherit;
    border-radius: inherit;
    border-width: 3px;
    box-sizing: border-box;
  }
}

.selectable::before {
  border-color: red;
}

.selected::before {
  border-color: yellow;
}

.v-enter-active, .v-leave-active {
  transition: all 0.2s;
}

.v-enter-from, .v-leave-to {
  opacity: 0;
}

@media screen and (max-width: 1024px) {
  .result {
    &__text {
      font-size: 12vmin;
    }
  }

  .retry {
    display: table;
    width: calc(100% - 2vmin);
    height: unset;
    font-size: 5vmin;
    padding: 1vmin 2vmin;
  }

  .text {
    font-size: 6vmin;
    margin-top: 6vmin;
  }

  .enter-btn {
    width: calc(100% - 2vmin);
    height: unset;
    font-size: 5vmin;
    margin-top: 6vmin;
    padding: 1vmin 2vmin;

    &:disabled {
      background-color: darkgray;
    }
  }

  .links {
    position: static;
    flex-direction: column-reverse;
    gap: 2vmin;
    width: 100%;
    margin: 4vmin;
    padding: 2vmin;

    &__btn {
      text-align: center;
      font-size: 4vmin;
    }
  }
}
</style>

<script lang="ts" setup>
import { onMounted, ref } from "vue";
import Board from "./components/Board.vue"
import Pocket from "./components/Pocket.vue"
import Stone from "./components/Stone.vue"
import RulePDF from "./assets/mancala_basic.pdf"

const pockets = ref<string[][]>([]) // ポケットリスト
const turn = ref<number>(1) // 現在のターン
const text = ref<string>("") // テキスト
const selectedIndex = ref<number>(-1) // 選択したポケットの番号
const isProcessing = ref<boolean>(false) // 処理中フラグ
const isFinishid = ref<boolean>(false) // 終了フラグ

const init = () => {
  // 色定義
  const colors: string[] = [
    "red",
    "green",
    "blue",
    "cyan",
    "yellow",
    "magenta",
  ]

  // ポケットリストを初期化
  pockets.value.splice(0)
  Array(14).fill(null).map(() => {
    pockets.value.push([])    
  })

  // 各ポケットに4つずつランダムな色の石を入れる
  pockets.value.map(pocket => {
    [...Array(4)].map(() => {
      pocket.push(colors[Math.floor(Math.random() * colors.length)])
    })
  })

  // 7番と14番のポケットを空にする
  // [Point] Vueで配列を変更する際、メソッドを使わないと変更が検知されないためビューが更新されない
  pockets.value[6].splice(0) // 7番のポケットの空にする
  pockets.value[13].splice(0) // 14番のポケットの空にする

  // 各変数の初期化
  turn.value = 1
  text.value = turn.value + "Pのターンです"
  selectedIndex.value = -1
  isProcessing.value = false
  isFinishid.value = false
}

// 待機
const sleep = async (ms: number) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(null)
    }, ms);
  })
}

// 終了判定
const judgement = (): boolean => {
  return [...Array(6)].every((_, i) => !pockets.value[i].length) || [...Array(6)].every((_, i) => !pockets.value[i + 7].length)
}

// 次のターン
const nextTurn = () => {
  if (turn.value === 1) {
    turn.value = 2
  } else {
    turn.value = 1
  }
}

// ポケットの選択
const selection = (index: number) => {
  if (selectedIndex.value === index) {
    selectedIndex.value = -1
    return
  }

  switch (turn.value) {
    case 1:
      if (0 <= index && index <= 5) {
        selectedIndex.value = index
      }
      break
    case 2:
      if (7 <= index && index <= 12) {
        selectedIndex.value = index
      }
      break
  }
}

// 決定ボタン
const decision = async () => {
  isProcessing.value = true
  let pocketNum: number = selectedIndex.value
  while (pockets.value[selectedIndex.value].length) {
    pocketNum = (pocketNum + 1) % pockets.value.length
    const stone = pockets.value[selectedIndex.value].pop()!
    pockets.value[pocketNum].push(stone)
    await sleep(500)
  }
  selectedIndex.value = -1
  if (judgement()) {
    isFinishid.value = true
    return
  }
  if (!(pocketNum === 6 || pocketNum === 13)) {
    nextTurn()
  }
  text.value = turn.value + "Pのターンです"
  isProcessing.value = false
}

// もう一度遊ぶ
const retry = () => {
  location.reload()
}

onMounted(() => {
  init()
})
</script>