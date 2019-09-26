<template>
  <div id="completed">
    <div class="jumbotron text-center">
      <h1 class="display-4">Quiz Complete!</h1>
      <p class="lead">Your Score is {{ correct }} out of {{ total }}</p>
      <button class="btn btn-primary btn-lg" @click="restart">Play Again</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Completed',
  props: {
    total: Number,
    correct: Number,
    restart: Function
  }
};
</script>

<style scoped>
#completed {
  padding-top: 50px;
}
</style>
