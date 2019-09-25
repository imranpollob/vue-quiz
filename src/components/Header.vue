<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <a class="navbar-brand" href="#">WoW Quiz</a>
      <button
        class="navbar-toggler"
        type="button"
        data-toggle="collapse"
        data-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav ml-auto">
          <li class="nav-item active">
            <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
          </li>
          <li class="nav-item"><span>Score</span>{{ correct }}/{{ total }}</li>
        </ul>
      </div>
    </nav>
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
