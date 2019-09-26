<template>
  <div>
    <nav class="navbar navbar-dark bg-dark">
      <a class="navbar-brand" href="#">WoW Quiz</a>
      <span class="navbar-text ml-auto text-light">Score {{ correct }}/{{ total }} </span>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'Header',
  props: {
    total: Number,
    correct: Number
  }
};
</script>

<style scoped></style>
