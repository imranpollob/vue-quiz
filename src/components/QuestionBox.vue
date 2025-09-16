<template>
  <article class="question-card">
    <header class="question-meta">
      <span class="badge difficulty">{{ difficultyLabel }}</span>
      <span v-if="question.category" class="badge category">{{ question.category }}</span>
    </header>

    <h2 class="question" v-html="question.question"></h2>

    <div class="answers">
      <button
        v-for="(answer, answerIndex) in answers"
        :key="`${answerIndex}-${answer}`"
        type="button"
        class="answer"
        :class="answerClass(answer)"
        v-html="answer"
        :disabled="answered"
        @click="selectAnswer(answer)"
      ></button>
    </div>

    <footer class="controls">
      <span class="progress">Question {{ index + 1 }} of {{ total }}</span>
      <button
        type="button"
        class="next"
        :disabled="!answered"
        @click="goToNext"
      >
        {{ index + 1 === total ? 'See my results' : 'Next question' }}
      </button>
    </footer>
  </article>
</template>

<script setup>
import { computed, ref, watch } from 'vue';

const props = defineProps({
  question: {
    type: Object,
    required: true
  },
  index: {
    type: Number,
    required: true
  },
  total: {
    type: Number,
    required: true
  }
});

const emit = defineEmits(['answer', 'next']);

const selectedAnswer = ref('');
const answered = ref(false);
const answers = ref([]);

watch(
  () => props.question,
  newQuestion => {
    if (!newQuestion) return;
    answers.value = Array.isArray(newQuestion.answers) && newQuestion.answers.length
      ? newQuestion.answers
      : shuffleAnswers([
          ...(newQuestion.incorrect_answers || []),
          newQuestion.correct_answer
        ]);
    selectedAnswer.value = '';
    answered.value = false;
  },
  { immediate: true }
);

const difficultyLabel = computed(() => {
  const raw = props.question?.difficulty;
  if (!raw) return 'Any difficulty';
  return raw.charAt(0).toUpperCase() + raw.slice(1);
});

const selectAnswer = answer => {
  if (answered.value) return;
  selectedAnswer.value = answer;
  answered.value = true;
  emit('answer', answer === props.question.correct_answer);
};

const goToNext = () => {
  if (!answered.value) return;
  emit('next');
};

const answerClass = answer => {
  if (!answered.value) return '';
  if (answer === props.question.correct_answer) return 'correct';
  if (answer === selectedAnswer.value) return 'incorrect';
  return 'muted';
};

const shuffleAnswers = options =>
  [...options]
    .map(option => ({ option, sort: Math.random() }))
    .sort((a, b) => a.sort - b.sort)
    .map(({ option }) => option);
</script>

<style scoped>
.question-card {
  background: white;
  border-radius: 24px;
  padding: 32px;
  box-shadow: 0 40px 80px -60px rgba(15, 23, 42, 0.55);
  max-width: 720px;
  width: 100%;
  margin: 0 auto;
  display: grid;
  gap: 24px;
}

.question-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.badge {
  display: inline-flex;
  align-items: center;
  font-size: 13px;
  padding: 6px 12px;
  border-radius: 999px;
  background: rgba(37, 99, 235, 0.08);
  color: #2563eb;
  font-weight: 600;
}

.difficulty {
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.category {
  background: rgba(124, 58, 237, 0.14);
  color: #7c3aed;
}

.question {
  font-size: 26px;
  line-height: 1.3;
  margin: 0;
}

.answers {
  display: grid;
  gap: 12px;
}

.answer {
  padding: 16px 18px;
  border-radius: 16px;
  border: 1px solid rgba(148, 163, 184, 0.4);
  background: #f8fafc;
  font-size: 16px;
  text-align: left;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease;
}

.answer:hover:not(:disabled) {
  transform: translateY(-2px);
  border-color: rgba(37, 99, 235, 0.5);
  box-shadow: 0 16px 30px -24px rgba(37, 99, 235, 0.65);
}

.answer:disabled {
  cursor: default;
  transform: none;
}

.answer.correct {
  background: rgba(74, 222, 128, 0.18);
  border-color: rgba(22, 163, 74, 0.5);
  color: #0f5132;
}

.answer.incorrect {
  background: rgba(248, 113, 113, 0.16);
  border-color: rgba(220, 38, 38, 0.5);
  color: #7f1d1d;
}

.answer.muted {
  opacity: 0.7;
}

.controls {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.progress {
  font-size: 14px;
  color: rgba(15, 23, 42, 0.7);
}

.next {
  padding: 12px 20px;
  border-radius: 999px;
  border: none;
  background: linear-gradient(135deg, #2563eb, #9333ea);
  color: white;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.next:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  box-shadow: none;
  transform: none;
}

.next:not(:disabled):hover {
  transform: translateY(-1px);
  box-shadow: 0 18px 30px -22px rgba(147, 51, 234, 0.85);
}

@media (max-width: 640px) {
  .question-card {
    padding: 24px;
  }

  .question {
    font-size: 22px;
  }

  .answers {
    gap: 10px;
  }
}
</style>
