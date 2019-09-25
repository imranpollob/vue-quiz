<template>
  <div>
    <b-nav tabs>
      <b-nav-item disabled>WoW Quiz</b-nav-item>
      <b-nav-item disabled>{{ correct }}/{{ total }}</b-nav-item>
    </b-nav>
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
