<template>
  <div class="app-shell">
    <Header
      :status="status"
      :score="score"
      :total="questionCount"
      :question-number="currentIndex"
      @restart="restart"
    />

    <section class="content">
      <QuizSetup v-if="status === 'idle'" @start="startQuiz" />

      <div v-else-if="status === 'loading'" class="state-card">
        <div class="spinner"></div>
        <p>Fetching fresh trivia…</p>
      </div>

      <div v-else-if="status === 'error'" class="state-card">
        <h2>Hmm, that didn't work</h2>
        <p>{{ errorMessage }}</p>
        <div class="state-actions">
          <button class="primary" type="button" @click="retryFetch">Try again</button>
          <button class="ghost" type="button" @click="restart">Change setup</button>
        </div>
      </div>

      <QuestionBox
        v-else-if="status === 'active' && currentQuestion"
        :question="currentQuestion"
        :index="currentIndex"
        :total="questionCount"
        @answer="handleAnswer"
        @next="goToNextQuestion"
      />

      <Completed
        v-else-if="status === 'complete'"
        :score="score"
        :total="questionCount"
        :settings="lastSettings"
        @play-again="playAgain"
        @change-settings="restart"
      />
    </section>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import axios from 'axios';

import Header from './components/Header.vue';
import QuestionBox from './components/QuestionBox.vue';
import Completed from './components/Completed.vue';
import QuizSetup from './components/QuizSetup.vue';

const status = ref('idle');
const questions = ref([]);
const currentIndex = ref(0);
const score = ref(0);
const errorMessage = ref('');
const lastSettings = ref(null);

const questionCount = computed(() => questions.value.length);
const currentQuestion = computed(() => questions.value[currentIndex.value]);

const shuffleAnswers = options =>
  [...options]
    .map(option => ({ option, sort: Math.random() }))
    .sort((a, b) => a.sort - b.sort)
    .map(({ option }) => option);

const startQuiz = async settings => {
  status.value = 'loading';
  errorMessage.value = '';
  lastSettings.value = { ...settings };

  try {
    const params = new URLSearchParams();
    params.append('amount', settings.amount);
    if (settings.difficulty) params.append('difficulty', settings.difficulty);
    if (settings.category) params.append('category', settings.category);

    const { data } = await axios.get('https://opentdb.com/api.php', { params });

    if (!data.results || !data.results.length) {
      throw new Error('No questions were available for that combination. Try different filters.');
    }

    const timestamp = Date.now();

    questions.value = data.results.map((question, index) => ({
      id: `${timestamp}-${index}`,
      ...question,
      answers: shuffleAnswers([
        ...(question.incorrect_answers || []),
        question.correct_answer
      ])
    }));

    currentIndex.value = 0;
    score.value = 0;
    status.value = 'active';
  } catch (error) {
    errorMessage.value = error.message || 'Unable to load questions right now.';
    status.value = 'error';
  }
};

const handleAnswer = isCorrect => {
  if (isCorrect) {
    score.value += 1;
  }
};

const goToNextQuestion = () => {
  if (currentIndex.value + 1 >= questionCount.value) {
    status.value = 'complete';
  } else {
    currentIndex.value += 1;
  }
};

const restart = () => {
  status.value = 'idle';
  questions.value = [];
  currentIndex.value = 0;
  score.value = 0;
  errorMessage.value = '';
};

const playAgain = () => {
  if (lastSettings.value) {
    startQuiz(lastSettings.value);
  } else {
    restart();
  }
};

const retryFetch = () => {
  if (lastSettings.value) {
    startQuiz(lastSettings.value);
  } else {
    status.value = 'idle';
  }
};
</script>

<style scoped>
.app-shell {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.content {
  flex: 1;
  display: grid;
  place-items: center;
  padding: 24px;
}

.state-card {
  background: white;
  border-radius: 24px;
  padding: 36px 40px;
  box-shadow: 0 40px 80px -60px rgba(15, 23, 42, 0.56);
  max-width: 440px;
  width: 100%;
  text-align: center;
  display: grid;
  gap: 16px;
  justify-items: center;
}

.state-card h2 {
  margin: 0;
}

.state-card p {
  margin: 0;
  color: rgba(15, 23, 42, 0.72);
}

.spinner {
  width: 54px;
  height: 54px;
  border-radius: 50%;
  border: 5px solid rgba(37, 99, 235, 0.2);
  border-top-color: #2563eb;
  animation: spin 1s linear infinite;
}

.state-actions {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
  justify-content: center;
}

.primary,
.ghost {
  padding: 12px 24px;
  border-radius: 999px;
  font-weight: 600;
  cursor: pointer;
  border: none;
  transition: transform 0.2s ease, box-shadow 0.2s ease, opacity 0.2s ease;
}

.primary {
  background: linear-gradient(135deg, #2563eb, #9333ea);
  color: white;
}

.primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 18px 30px -22px rgba(37, 99, 235, 0.9);
}

.ghost {
  background: transparent;
  color: #2563eb;
  border: 1px solid rgba(37, 99, 235, 0.3);
}

.ghost:hover {
  opacity: 0.8;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

@media (max-width: 640px) {
  .content {
    padding: 16px;
  }
}
</style>
