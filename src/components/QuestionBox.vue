<template>
  <div class="text-center">
    <div id="question">
      <p v-html="question.question" class="m-3"></p>
    </div>
    <div id="answers">
      <ul class="list-group w-50 m-auto">
        <li
          class="list-group-item"
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          v-html="answer"
          @click="selectedAnswer(index)"
          :class="[addClass(index)]"
          :disabled="answered"
        ></li>
      </ul>
    </div>

    <button @click="nextQuestionMethod" class="btn btn-primary mt-3">
      {{ total === index + 1 ? 'Finish' : 'Next' }}
    </button>
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
        return 'list-group-item-danger';
      if (index === this.correctIndex) return 'list-group-item-success';
      return '';
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
  cursor: pointer;
}
#question {
  height: 100px;
  font-size: 18px;
}
#answers {
  height: 300px;
}
</style>
