<template>
  <div>
    <h2 v-html="question.question"></h2>
    <b-list-group>
      <b-list-group-item
        v-for="(answer, index) in answers"
        :key="index"
        v-html="answer"
      ></b-list-group-item>
    </b-list-group>
    <button @click="nextQuestionMethod">Next</button>
  </div>
</template>

<script>
export default {
  name: 'QuestionBox',
  props: {
    question: Object,
    nextQuestionMethod: Function
  },
  computed: {
    answers() {
      return [...this.question.incorrect_answers, this.question.correct_answer];
    }
  }
};
</script>

<style scoped></style>
