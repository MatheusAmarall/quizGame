<template>
  <div>
    <ScoreBoard :winCount="winCount" :loseCount="loseCount"/>
    <div v-if="question">
      <h1 v-html="question"></h1>

      <div v-for="(answer, index) in answers" :key="index">
        <input :disabled="answerSubmited" type="radio" name="options" :value="answer" v-model="chosenAnswer">
        <label v-html="answer"></label><br>
      </div>

      <button v-show="!answerSubmited" @click="submitAnswer" class="send" type="button">Send</button>

      <section v-show="answerSubmited" class="result">
        <h4 v-if="chosenAnswer == correctAnswer"
        v-html='`&#9989; Congratulations, the answer "${correctAnswer}" is correct.`'></h4>
        <h4 v-else
        v-html='`&#10060; I´m sorry, you picked the wrong answer. The correct is "${correctAnswer}".`'></h4>
        <button @click="getNewQuestion" class="send" type="button">Next question</button>
      </section>

    </div>
  </div>
</template>

<script>
import ScoreBoard from './components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: [],
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmited: false,
      winCount: 0,
      loseCount: 0
    }
  },
  
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if(!this.chosenAnswer) {
        alert('Pick one of the options!')
      } else {
        this.answerSubmited = true;
        if(this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion() {
      this.answerSubmited = false
      this.chosenAnswer = undefined
      this.question = undefined

      this.axios
      .get("https://opentdb.com/api.php?amount=1&category=18")
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
    }
  },
  created() {
    this.getNewQuestion()
  }
}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
