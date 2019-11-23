<template>
  <section class="container">
    <div>
      <h1 class="title">{{ gameTitle }}</h1>
      <h2>
        HP:{{ hp }}<meter class="meter" v-bind:value="hp" max="10" min="0" low="3" high="7" optimum="8"></meter>
      </h2>
      <img src="/kaiju.png" width="200" height="300"/>
      <h1 v-if="time" class="subtitle">{{ getTime() }}</h1>
      <h1 class="subtitle">{{ getQuestion() }}</h1>
      <h2 class="subtitle">{{ answer }}</h2>
      <input @keyup.space="start()" v-model="answer" class="input">
      <div class="links">
        <a target="_blank" @click="reset()" class="button--green">リセット</a>
      </div>
    </div>
  </section>
</template>

<script>
  import lodash from 'lodash'


export default {
  data() {
    const words = ["html", "css", "php", "java", "javascript", "ruby", "python", "swift", "unity", "cobol","go", "perl", "sql"]
    const questionNum = 11
    return {
      allWords: words,
      words: lodash.shuffle(words),
      status: "ready",
      questionNum: questionNum,
      question: "",
      answerNum: 0,
      wordCount: 0,
      answer: "",
      timer: undefined,
      time: 0,
      hp:10,
      gameTitle : "タイピングで怪獣を倒そう！"
    }
  },

  methods: {
    start(){
      if(this.status == "ready"){
        this.status = "started"
        this.timer = setInterval(this.timerCount, 100)
        this.answer = ""
      }
    },

    timerCount(){
      this.time += 0.1
    },

    getTime(){
      if(this.isFinished()){
        return Number((this.time).toFixed(1)) + "秒かかりました"
      }
      return Number((this.time).toFixed(1))
    },

    getQuestion(){
      if(this.status == "ready"){
        return "スペースキーを押してください"
      }

      if(this.isFinished()){
        this.status = "finished"
        clearInterval(this.timer)
        this.hp = 0
        this.gameTitle = "おめでとう！怪獣を倒しました!!"
        var speed = Number(this.wordCount / this.time * 60).toFixed(1)
        return "あなたのタイピング速度は:" + speed + "WPM"
      }

      if(this.checkAnswer()){
        this.answerNum += 1
        this.wordCount += this.question.length
        this.question = this.words.pop()
        this.answer = ""
      }
      return this.question
    },

    isFinished(){
      if(this.answerNum >= this.questionNum){
        return true
      }
      return false
    },

    checkAnswer(){
      if(this.calculate()){
        this.hp -= 1
      }
      return this.question == this.answer
    },

    calculate(){
      if(this.question == this.answer && this.answer.length > 0){
        return true
      }
    },

    reset(){
      //質問、回答、質問箱、タイマー、時間、状態、回答数、
      this.question = ""
      this.answer = ""
      this.words = lodash.shuffle(this.allWords)
      this.status = "ready"
      clearInterval(this.timer)
      this.time = 0
      this.answerNum = 0
      this.wordCount = 0
      this.hp = 10
      this.gameTitle = "タイピングで怪獣を倒そう！"
    }
  }
}
</script>

<style>
  .container {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
  }
  .title {
    font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
      'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
    display: block;
    font-weight: 300;
    font-size: 52px;
    color: #353b5e;
    letter-spacing: 1px;
  }
  .subtitle {
    font-weight: 300;
    font-size: 32px;
    color: #526488;
    word-spacing: 5px;
    padding-bottom: 15px;
  }
  .links {
    padding-top: 15px;
  }
  .input{
    height: 30px;;
  }
  .meter{
    width: 180px;
    height: 30px;
  }
</style>