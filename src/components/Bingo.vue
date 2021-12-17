<template>
  <div class="notouch py-5">
    <div class="bingo-cards-wrapper" v-if="answers.length > 0">
      <div class="bingo-grid-row">
        <div class="bingo-card-header bg-danger">
          <p class="mb-0">B</p>
        </div>
        <div class="bingo-card-header bg-warning">
          <p class="mb-0">I</p>
        </div>
        <div class="bingo-card-header bg-success">
          <p class="mb-0">N</p>
        </div>
        <div class="bingo-card-header bg-info">
          <p class="mb-0">G</p>
        </div>
        <div class="bingo-card-header bg-primary">
          <p class="mb-0">O</p>
        </div>
      </div>
      <div class="bingo-grid-row" v-bind:key="y" v-for="y in tableHeight">
        <div class="bingo-card" id="pc"
             @click="answerClick(answers[(y - 1)*tableHeight + (x-1)])"
             :class="isActive(answers[(y - 1)*tableHeight + (x-1)]) ? 'active' : ''"
             v-bind:key="x"
             v-for="x in tableWidth">
          <p class="mb-0" v-if="x === tableWidth-4">{{ numbers_B[(y-1)].answer }}</p>
          <p class="mb-0" v-else-if="x === tableWidth-3">{{ numbers_I[(y-1)].answer }}</p>
          <p class="mb-0" v-else-if="x === tableWidth-2"><strong v-if="y === tableHeight-2">★</strong><strong v-else>{{ numbers_N[(y-1)].answer }}</strong></p>
          <p class="mb-0" v-else-if="x === tableWidth-1">{{ numbers_G[(y-1)].answer }}</p>
          <p class="mb-0" v-else-if="x === tableWidth">{{ numbers_O[(y-1)].answer }}</p>
        </div>
      </div>
    </div>
    <!-- <button type="button" class="btn btn-bingo mx-auto d-block mt-5" @click="newGame">
      やり直し!
    </button> -->
    <h3>
      <div id="createTime" class="createTime mx-auto col-12" style="width:300px"></div>
    </h3>
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
      
      if(this.isReach){
        alert(this.isReach + '列リーチ！')
      }

      const bingoLines = this.isBingo
      if (bingoLines.length) { 
        alert('ビンゴ！');
        
        var answersInCard = [];
        for(var row = 0; row < 5; row++){
          answersInCard.push(this.numbers_B[row].answer);
          answersInCard.push(this.numbers_I[row].answer);
          answersInCard.push(this.numbers_N[row].answer);
          answersInCard.push(this.numbers_G[row].answer);
          answersInCard.push(this.numbers_O[row].answer);
        }

        answersInCard[bingoLines[0].answers[2].index] = bingoLines[0].answers[2].index != 12 ? answersInCard[bingoLines[0].answers[2].index] : 'FREE' 

        const user = prompt('おめでとうございます！名前を入力してください');
        const answers = this.answers.filter((a) => a.count === 1);
        const formData = new FormData();
        formData.append('token', 'xoxb-2070188486354-2843107345683-YwzugFb6f4dgifSMfjCgIN4u');
        formData.append('channel', 'bingo');
        formData.append('text', 'BINGO!! from ' + user + ', '   //ユーザー名
        + answersInCard[bingoLines[0].answers[0].index] + ', '                              //ビンゴ当たり目 
        + answersInCard[bingoLines[0].answers[1].index] + ', '  
        + answersInCard[bingoLines[0].answers[2].index] + ', '  
        + answersInCard[bingoLines[0].answers[3].index] + ', '  
        + answersInCard[bingoLines[0].answers[4].index] + ', '  
        + document.getElementById("createTime").innerHTML);     //カード作成日
        fetch('https://slack.com/api/chat.postMessage', {method: 'POST', body: formData});  //送信
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

      let now = new Date();
      document.getElementById("createTime").innerHTML = `作成時刻：${now.getHours()}:${now.getMinutes().toString().padStart(2, "0")}:${now.getSeconds().toString().padStart(2, "0")}`
    },
  },
  computed: {
    isBingo() {
          //チェック済みのセル取得
          const answers = this.answers.filter((a) => a.count === 1).map((element) => {
            return {
              index: _.indexOf(this.answers, element),
              answer: element
            }
          });
          //列に対するビンゴ判定
          var bingoColLines = [];
          var colAnswers = [];
          for(var col = 0; col < 5; col++){
            colAnswers = answers.filter((element) => _.floor(element.index / 5, 0) == col);
            if(colAnswers.length == 5){
              bingoColLines.push({
                col: col,
                answers: colAnswers
              });
            }
          }
          //行に対するビンゴ判定
          var bingoRowLines = [];
          var rowAnswers = [];
          for(var row = 0; row < 5; row++){
            rowAnswers = answers.filter((element) => element.index % 5 == row);
            if(rowAnswers.length == 5){
              bingoRowLines.push({
                row: row,
                answers: rowAnswers
              });
            }
          }
          //対角に対するビンゴ判定
          var bingoXLines = [];
          const x1Answers = answers.filter((element) => _.floor(element.index / 5, 0) == element.index % 5);
          const x2Answers = answers.filter((element) => _.floor(element.index / 5, 0) == (4 - element.index % 5));
          if(x1Answers.length == 5){
            bingoXLines.push({
              x: 1,
              answers: x1Answers
            });
          }
          if(x2Answers.length == 5){
            bingoXLines.push({
              x: 2,
              answers: x2Answers
            });
          }
          return bingoRowLines.concat(bingoColLines.concat(bingoXLines));
    },
    isReach() {
      //チェック済みのセル取得
      const answers = this.answers.filter((a) => a.count === 1).map((element) => {
        return {
          index: _.indexOf(this.answers, element),
          answer: element
        }
      });
      //列に対するリーチ判定
      var ReachColLines = [];
      var colAnswers = [];
      for(var col = 0; col < 5; col++){
        colAnswers = answers.filter((element) => _.floor(element.index / 5, 0) == col);
        if(colAnswers.length == 4){
          ReachColLines.push({
            col: col,
            answers: colAnswers
          });
        }
      }
      //行に対するリーチ判定
      var ReachRowLines = [];
      var rowAnswers = [];
      for(var row = 0; row < 5; row++){
        rowAnswers = answers.filter((element) => element.index % 5 == row);
        if(rowAnswers.length == 4){
          ReachRowLines.push({
            row: row,
            answers: rowAnswers
          });
        }
      }
      //対角に対するリーチ判定
      var ReachXLines = [];
      const x1Answers = answers.filter((element) => _.floor(element.index / 5, 0) == element.index % 5);
      const x2Answers = answers.filter((element) => _.floor(element.index / 5, 0) == (4 - element.index % 5));
      if(x1Answers.length == 4){
        ReachXLines.push({
          x: 1,
          answers: x1Answers
        });
      }
      if(x2Answers.length == 4){
        ReachXLines.push({
          x: 2,
          answers: x2Answers
        });
      }
      return ReachRowLines.length + ReachColLines.length + ReachXLines.length;
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

.bingo-card-header {
  margin: 5px;
  /* cursor: pointer; */
  text-align: center;
  background-color: hsla(0, 0%, 100%, 0.95);
  /* background-image: linear-gradient(0deg, hsla(0, 0%, 70.6%, .8), hsla(0, 0%, 100%, 0.25)); */
  /* border: 5px solid #c00; */
  font-weight: bold;
  font-family: Lucida Sans Unicode, Lucida Grande, sans-serif;
  font-size: 18px;
  word-wrap: break-word;
  background-repeat: no-repeat;
  background-position: 50%;
  background-size: cover;
  padding: 5px 10px;
  color: yellow;
  flex: 1 1 0;
  display: flex;
  align-items: center;
  justify-content: center;
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

.createTime {
  border:solid 5px #cc0000;
  background-color: #ff9911;
  color: #eee;
  font-weight: bold;
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
@media (min-width: 768px) {
   /* 横幅が800px以上の場合に適用するスタイル */
   div#pc {
      height: 100px;
      font-size: 30px;
      }
}
</style>
