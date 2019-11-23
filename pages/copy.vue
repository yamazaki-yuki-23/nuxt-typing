<template>
  <section class="container">
    <div>
      <h1 v-if="time" class="subtitle">{{ getTime() }}</h1>
      <h1 class="subtitle">{{ getQuestion() }}</h1>
      <h2 class="subtitle">{{ answer }}</h2>
      <input @keyup.space="start()" v-model="answer">
      <div class="links">
        <a target="_blank" @click="reset()" class="button--green">リセット</a>
      </div>
    </div>
  </section>
</template>

<script>
import Logo from '~/components/Logo.vue'
import lodash from 'lodash'

export default {
  components: {
    Logo
  },

  data() {
    const words = ["get", "this", "for", "push", "controller", "config", "error", "try", "def", "components"]
    const questionNum = 10
    return {
      allWords: words,
      words: lodash.shuffle(words),
      status: "ready",
      questionNum: questionNum,
      question: "",
      answeredNum: 0,
      wordCount: 0,
      answer: "",
      timer: undefined,
      time: 0
    }
  },

  methods: {
    //タイマーの起動
    start(){
      this.status = "started"
      this.timer = setInterval(this.timerCount,100)
      this.answer = ""
    },

    //タイムカウント
    timerCount(){
      this.time += 0.1
    },

    //所要時間の取得(小数一桁まで)
    getTime(){
      return Number((this.time).toFixed(1))
    },

    getQuestion(){
      if(this.status == "ready"){
        return "スペースキーを押してください"
      }

      if(this.isFinished()){
        this.status = "finished"
        clearInterval(this.timer)
        var speed = Number(this.wordCount / this.time * 60).toFixed(1)
        return "タイピング速度:" + speed + "WPM"
      }

      if(this.checkAnswer()){
        this.answeredNum += 1
        this.wordCount += this.question.length
        this.question = this.words.pop()
        this.answer = ""
      }
      return this.question
    },

    isFinished(){
      if(this.answeredNum >= this.questionNum){
        return true
      }
      return false
    },

    checkAnswer(){
      return this.question == this.answer
    },

    reset(){
      this.question = ""
      this.answer = ""
      this.words = lodash.shuffle(this.allWords)
      clearInterval(this.timer)
      this.time = 0
      this.status = "ready"
      this.answeredNum = 0
      this.wordCount = 0
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
  .links {
  padding-top: 15px;
  }
  .title {
  font-family: 'Quicksand', 'Source Sans Pro', '-apple-system', 'BlinkMacSystemFont',
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}
.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}
</style>