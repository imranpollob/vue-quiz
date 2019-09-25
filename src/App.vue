<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <Header></Header>
    <QuestionBox></QuestionBox>
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
      questions: []
    };
  },
  mounted() {
    fetch('https://opentdb.com/api.php?amount=50')
      .then(response => response.json())
      .then(data => {
        console.log(data);
        this.questions = data.results;
      });
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
