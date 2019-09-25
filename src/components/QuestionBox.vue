<template>
  <div>
    <h2 v-html="question.question"></h2>
    <b-list-group>
      <b-list-group-item
        v-for="(answer, index) in shuffledAnswers"
        :key="index"
        v-html="answer"
        @click="selectedAnswer(index)"
      ></b-list-group-item>
    </b-list-group>
    <button @click="nextQuestionMethod">Next</button>
  </div>
</template>

<script>
import _ from 'lodash';

export default {
  name: 'QuestionBox',
  props: {
    question: Object,
    nextQuestionMethod: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: []
    };
  },
  computed: {
    answers() {
      return [...this.question.incorrect_answers, this.question.correct_answer];
    }
  },
  methods: {
    selectedAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      this.shuffledAnswers = _.shuffle(this.answers);
    }
  },
  watch: {
    question: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
      }
    }
  }
};
</script>

<style scoped>
.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightblue;
}

.incorrect {
  background-color: lightblue;
}
</style>
