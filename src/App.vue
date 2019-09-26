<template>
  <div id="app" class="container">
    <div v-if="!selected" id="selector" class="text-center">
      Select number of questions you want to answer:
      <select v-model="selected" @change="getQuestions">
        <option value="5">5</option>
        <option value="10">10</option>
        <option value="15">15</option>
        <option value="20">20</option>
      </select>
    </div>
    <div v-if="selected">
      <Header :total="parseInt(selected)" :correct="point"></Header>
      <QuestionBox
        v-if="questions.length"
        :question="questions[index]"
        :nextQuestionMethod="nextQuestion"
        :gotPointMethod="gotPoint"
        :total="parseInt(selected)"
        :index="index"
      ></QuestionBox>
    </div>
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
      index: 0,
      selected: null,
      point: 0
    };
  },
  methods: {
    nextQuestion() {
      this.index++;
      if (this.index >= this.selected) {
        this.selected = null;
        this.index = 0;
        this.point = 0;
        this.questions = [];
      }
    },
    async getQuestions() {
      try {
        let res = await fetch(`https://opentdb.com/api.php?amount=${this.selected}`);
        res = await res.json();
        this.questions = res.results;
      } catch (e) {
        console.log(e);
      }
    },
    gotPoint() {
      this.point++;
    }
  }
};
</script>

<style></style>
