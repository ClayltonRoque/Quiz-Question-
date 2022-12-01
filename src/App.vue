<script>
import ScoreBoard from "./components/ScoreBoard.vue";
export default {
  name: "App",
  components: {
    ScoreBoard,
  },

  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswers: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    };
  },

  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(
        Math.round(Math.random() * answers.length),
        0,
        this.correctAnswers
      );
      console.log(this.correctAnswers);
      return answers;
    },
  },

  methods: {
    submitAnswer() {
      if (this.chosenAnswer == undefined) {
        alert("por favor selecionar alguma resposta");
      } else {
        this.answerSubmitted = true;
        if (this.chosenAnswer != this.correctAnswers) {
          this.loseCount += 1;
        }
        if (this.chosenAnswer == this.correctAnswers) {
          this.winCount += 1;
        }
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios
        .get("https://opentdb.com/api.php?amount=1&category=18")
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswers = response.data.results[0].correct_answer;
        });
    },
  },

  created() {
    this.getNewQuestion();
  },
};
</script>

<template>
  <div>
    <template v-if="this.question">
      <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />
      <h1 v-html="this.question"></h1>
      <template v-for="(answer, index) in this.answers" v-bind:key="index">
        <input
          :disabled="this.answerSubmitted"
          type="radio"
          name="options"
          :value="answer"
          v-model="this.chosenAnswer"
        />
        <label v-html="answer"></label> <br />
      </template>
      <button
        class="send"
        type="button"
        @click="this.submitAnswer()"
        :hidden="this.answerSubmitted"
      >
        Send
      </button>

      <section class="result" :hidden="!this.answerSubmitted">
        <h4 v-if="this.chosenAnswer == this.correctAnswers">
          &#9989; "acertou"
        </h4>
        <h4
          v-if="this.chosenAnswer != this.correctAnswers"
          v-html="
            '&#10060; errou, errou feio errou rude, a resposta correta é: ' +
            this.correctAnswers +
            ' desu <3'
          "
        ></h4>
        <button class="send" type="button" @click="getNewQuestion()">
          Next question
        </button>
      </section>
    </template>
  </div>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Halvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type-radio] {
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
    cursor: po0inter;
  }
}
</style>
