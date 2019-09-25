<template>
  <div>
    <h2 v-html="question.question"></h2>
    <b-list-group>
      <b-list-group-item
        v-for="(answer, index) in shuffledAnswers"
        :key="index"
        v-html="answer"
        @click="selectedAnswer(index)"
        :class="[addClass(index)]"
        :disabled="answered"
      ></b-list-group-item>
    </b-list-group>
    <button @click="nextQuestionMethod">{{ total === index + 1 ? 'Finish' : 'Next' }}</button>
  </div>
</template>

<script>
import _ from 'lodash';

export default {
  name: 'QuestionBox',
  props: {
    question: Object,
    nextQuestionMethod: Function,
    gotPointMethod: Function,
    total: Number,
    index: Number
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      answered: false
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
      this.answered = true;

      if (index === this.correctIndex) {
        this.gotPointMethod();
      }
    },
    shuffleAnswers() {
      this.shuffledAnswers = _.shuffle(this.answers);
      this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer);
    },
    addClass(index) {
      if (!this.answered) return '';
      if (index === this.selectedIndex && this.selectedIndex !== this.correctIndex)
        return 'incorrect';
      if (index === this.correctIndex) return 'correct';
    }
  },
  watch: {
    question: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  }
};
</script>

<style scoped>
.list-group-item:hover {
  /* background: #EEE; */
  cursor: pointer;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}
</style>
