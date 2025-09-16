<template>
  <header class="app-header" :class="status">
    <div class="brand">
      <span class="mark">Q</span>
      <div>
        <strong>Vue Quiz</strong>
        <small>Your daily trivia boost</small>
      </div>
    </div>

    <div class="meta">
      <div v-if="showProgress" class="progress">
        <div class="track">
          <div class="bar" :style="{ width: progressWidth }"></div>
        </div>
        <span>{{ questionNumber + 1 }} / {{ total }}</span>
      </div>

      <div v-if="showScore" class="score">
        <span>Score</span>
        <strong>{{ score }}<span v-if="total">/{{ total }}</span></strong>
      </div>

      <button v-if="canRestart" type="button" class="restart" @click="emit('restart')">
        Start over
      </button>
    </div>
  </header>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  status: {
    type: String,
    required: true
  },
  score: {
    type: Number,
    default: 0
  },
  total: {
    type: Number,
    default: 0
  },
  questionNumber: {
    type: Number,
    default: 0
  }
});

const emit = defineEmits(['restart']);

const showProgress = computed(() => props.status === 'active' && props.total > 0);
const showScore = computed(() => ['active', 'complete'].includes(props.status));
const canRestart = computed(() => ['active', 'complete', 'error'].includes(props.status));

const progressWidth = computed(() => {
  if (!showProgress.value) return '0%';
  const progress = ((props.questionNumber + 1) / props.total) * 100;
  return `${Math.min(progress, 100)}%`;
});
</script>

<style scoped>
.app-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 24px 32px;
  color: #0f172a;
}

.brand {
  display: flex;
  align-items: center;
  gap: 12px;
}

.mark {
  width: 44px;
  height: 44px;
  border-radius: 14px;
  background: linear-gradient(135deg, #2563eb, #9333ea);
  color: white;
  display: grid;
  place-items: center;
  font-weight: 700;
  font-size: 22px;
}

.brand strong {
  font-size: 18px;
  display: block;
}

.brand small {
  font-size: 13px;
  color: rgba(15, 23, 42, 0.72);
}

.meta {
  display: flex;
  align-items: center;
  gap: 24px;
}

.score {
  text-align: center;
  color: #0f172a;
}

.score span {
  display: block;
  font-size: 13px;
  color: rgba(15, 23, 42, 0.7);
}

.score strong {
  font-size: 20px;
}

.progress {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 160px;
}

.track {
  position: relative;
  flex: 1;
  height: 6px;
  background: rgba(148, 163, 184, 0.35);
  border-radius: 999px;
  overflow: hidden;
}

.bar {
  height: 100%;
  background: linear-gradient(135deg, #2563eb, #9333ea);
  border-radius: inherit;
  transition: width 0.3s ease;
}

.restart {
  padding: 10px 16px;
  border-radius: 999px;
  border: none;
  background: rgba(37, 99, 235, 0.12);
  color: #2563eb;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s ease, transform 0.2s ease;
}

.restart:hover {
  background: rgba(37, 99, 235, 0.2);
  transform: translateY(-1px);
}
</style>
