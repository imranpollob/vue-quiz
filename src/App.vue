<template>
  <div id="app" class="container">
    <completed
      v-if="completed"
      :total="parseInt(selected)"
      :correct="point"
      :restart="restart"
    ></completed>
    <div v-else>
      <div v-if="!selected" id="selector" class="text-center">
        Select number of questions you want to answer:
        <select v-model="selected" @change="getQuestions">
          <option value="5">5</option>
          <option value="10">10</option>
          <option value="15">15</option>
          <option value="20">20</option>
        </select>
      </div>
      <div v-else>
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
  </div>
</template>

<script>
import Header from './components/Header';
import QuestionBox from './components/QuestionBox';
import Completed from './components/Completed';

export default {
  name: 'app',
  components: {
    Header,
    QuestionBox,
    Completed
  },
  data() {
    return {
      questions: [],
      index: 0,
      selected: null,
      point: 0,
      completed: false
    };
  },
  methods: {
    restart() {
      this.completed = false;
      this.selected = null;
      this.index = 0;
      this.point = 0;
      this.questions = [];
    },
    nextQuestion() {
      this.index++;
      if (this.index >= this.selected) {
        this.completed = true;
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

<style>
#selector {
  padding-top: 50px;
  font-size: 20px;
}
</style>
