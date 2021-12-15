<template>
  <div class="notouch py-5">
    <div class="bingo-cards-wrapper" v-if="answers.length > 0">
      <div class="bingo-grid-row" v-bind:key="y" v-for="y in tableHeight">
        <div class="bingo-card"
             @click="answerClick(answers[(y - 1)*tableHeight + (x-1)])"
             :class="isActive(answers[(y - 1)*tableHeight + (x-1)]) ? 'active' : ''"
             v-bind:key="x"
             v-for="x in tableWidth">
          <p class="mb-0" v-if="x === tableWidth-4">{{ numbers_B[(y-1)].answer }}</p>
          <p class="mb-0" v-else-if="x === tableWidth-3">{{ numbers_I[(y-1)].answer }}</p>
          <p class="mb-0" v-else-if="x === tableWidth-2"><strong v-if="y === tableHeight-2">FREE</strong><strong v-else>{{ numbers_N[(y-1)].answer }}</strong></p>
          <p class="mb-0" v-else-if="x === tableWidth-1">{{ numbers_G[(y-1)].answer }}</p>
          <p class="mb-0" v-else-if="x === tableWidth">{{ numbers_O[(y-1)].answer }}</p>
        </div>
      </div>
    </div>
    <button type="button" class="btn btn-bingo mx-auto d-block mt-5" @click="newGame">
      やり直し!
    </button>
  </div>
</template>

<script>
import BingoElements from '../assets/items.json'
import _ from 'lodash'

export default {
  mounted() {
    this.newGame()
  },
  data() {
    return {
      tableWidth: 5,
      tableHeight: 5,
      answers: [],
      numbers_B: [],
      numbers_I: [],
      numbers_N: [],
      numbers_G: [],
      numbers_O: [],
    }
  },
  updated(){
    this.answers[12].count = 1;
  },
  methods: {
    isActive(element) {
      return element.count > 0
    },
    answerClick(element) {
      if(element.count === 1){
        element.count--;
      }else{
        element.count++;
      }

      if (this.isBingo) {
        alert('ビンゴ！')
      }
    },
    newGame() {
      const bingoAnswers = BingoElements.answers;
      const bingoNumbers_B = BingoElements.row_B;
      const bingoNumbers_I = BingoElements.row_I;
      const bingoNumbers_N = BingoElements.row_N;
      const bingoNumbers_G = BingoElements.row_G;
      const bingoNumbers_O = BingoElements.row_O;

      this.answers = _.shuffle(bingoAnswers).slice(0, this.tableHeight * this.tableWidth).map((element) => {
        return {
          count: 0,
          answer: element
        }
      })
      this.numbers_B = _.shuffle(bingoNumbers_B).slice(0, this.tableHeight).map((element) => {
        return {
          count: 0,
          answer: element
        }
      })
      this.numbers_I = _.shuffle(bingoNumbers_I).slice(0, this.tableHeight).map((element) => {
        return {
          count: 0,
          answer: element
        }
      })
      this.numbers_N = _.shuffle(bingoNumbers_N).slice(0, this.tableHeight).map((element) => {
        return {
          count: 0,
          answer: element
        }
      })
      this.numbers_G = _.shuffle(bingoNumbers_G).slice(0, this.tableHeight).map((element) => {
        return {
          count: 0,
          answer: element
        }
      })
      this.numbers_O = _.shuffle(bingoNumbers_O).slice(0, this.tableHeight).map((element) => {
        return {
          count: 0,
          answer: element
        }
      })
    },
  },
  computed: {
    isBingo() {
      const answers = this.answers.filter((a) => a.count === 0);
      return !answers.length;
    }
  }
}
</script>

<style>
.bingo-cards-wrapper {
  padding: 5px;
  margin: 0 auto;
}

.bingo-grid-row {
  display: flex;
}

.bingo-card {
  margin: 5px;
  cursor: pointer;
  text-align: center;
  background-color: hsla(0, 0%, 100%, 0.95);
  background-image: linear-gradient(0deg, hsla(0, 0%, 70.6%, .8), hsla(0, 0%, 100%, 0.25));
  border: 5px solid #c00;
  font-weight: bold;
  font-family: Lucida Sans Unicode, Lucida Grande, sans-serif;
  font-size: 18px;
  word-wrap: break-word;
  background-repeat: no-repeat;
  background-position: 50%;
  background-size: cover;
  padding: 5px 10px;
  color: #333;
  flex: 1 1 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.bingo-card:hover {
  border: 5px solid #e00;
  color: #111;
}

.bingo-card.active {
  background-color: hsla(0, 0%, 66.7%, 0.85);
  border-color: #f22;
  animation-name: bingo;
  animation-duration: 2s;
  animation-iteration-count: infinite;
}

.btn-bingo {
  border-color: #eee;
  background-color: #cc0000;
  color: #eee;
  font-weight: bold;
}

.btn-bingo:focus {
  box-shadow: none;
}

.btn-bingo:hover {
  color: #eee;
  background-color: #ee0000;
}

@keyframes bingo {
  0% {
    border-color: #ff2222;
  }

  25% {
    border-color: #ee0000;
  }

  50% {
    border-color: #ff9911;
  }

  100% {
    border-color: #ff2222;
  }
}
</style>
