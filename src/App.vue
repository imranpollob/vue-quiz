<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <Header></Header>
    <QuestionBox
      v-if="questions.length"
      :question="questions[index]"
      :nextQuestionMethod="nextQuestion"
    ></QuestionBox>
  </div>
</template>

<script>
import Header from './components/Header';
import QuestionBox from './components/QuestionBox';

export default {
  name: 'app',
  components: {
    Header,
    QuestionBox
  },
  data() {
    return {
      questions: [],
      index: 0
    };
  },
  mounted() {
    fetch('https://opentdb.com/api.php?amount=50')
      .then(response => response.json())
      .then(data => {
        this.questions = data.results;
      });
  },
  methods: {
    nextQuestion() {
      this.index++;
    }
  }
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
