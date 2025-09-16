<template>
  <section class="summary-card">
    <h2>All done!</h2>
    <p class="headline">
      You answered <strong>{{ score }}</strong> out of <strong>{{ total }}</strong> questions correctly.
    </p>

    <div class="stat">
      <span>Accuracy</span>
      <strong>{{ accuracy }}%</strong>
    </div>

    <ul class="details">
      <li>
        <span>Questions</span>
        <strong>{{ total }}</strong>
      </li>
      <li>
        <span>Difficulty</span>
        <strong>{{ difficultyLabel }}</strong>
      </li>
      <li>
        <span>Category</span>
        <strong>{{ settings?.categoryName || 'Any category' }}</strong>
      </li>
    </ul>

    <div class="actions">
      <button class="primary" type="button" @click="emit('play-again')">Play again</button>
      <button class="ghost" type="button" @click="emit('change-settings')">Change setup</button>
    </div>
  </section>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  score: {
    type: Number,
    required: true
  },
  total: {
    type: Number,
    required: true
  },
  settings: {
    type: Object,
    default: null
  }
});

const emit = defineEmits(['play-again', 'change-settings']);

const accuracy = computed(() => {
  if (!props.total) return 0;
  return Math.round((props.score / props.total) * 100);
});

const difficultyLabel = computed(() => {
  const difficulty = props.settings?.difficulty;
  if (!difficulty) return 'Any difficulty';
  return difficulty.charAt(0).toUpperCase() + difficulty.slice(1);
});
</script>

<style scoped>
.summary-card {
  background: white;
  border-radius: 24px;
  padding: 36px 40px;
  box-shadow: 0 40px 80px -60px rgba(15, 23, 42, 0.56);
  max-width: 560px;
  width: 100%;
  margin: 0 auto;
  text-align: center;
}

h2 {
  margin: 0 0 8px;
  font-size: 30px;
}

.headline {
  margin: 0 0 28px;
  color: rgba(15, 23, 42, 0.75);
}

.stat {
  background: linear-gradient(135deg, rgba(37, 99, 235, 0.12), rgba(147, 51, 234, 0.12));
  border-radius: 18px;
  padding: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 24px;
}

.stat span {
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  color: #2563eb;
}

.stat strong {
  font-size: 28px;
  font-weight: 700;
  color: #1e293b;
}

.details {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 16px;
  margin: 0 0 32px;
  padding: 0;
  list-style: none;
}

.details li {
  background: rgba(15, 23, 42, 0.03);
  border-radius: 18px;
  padding: 18px 16px;
  display: grid;
  gap: 6px;
}

.details span {
  font-size: 13px;
  color: rgba(71, 85, 105, 0.9);
  letter-spacing: 0.04em;
  text-transform: uppercase;
}

.details strong {
  font-size: 16px;
  color: #0f172a;
}

.actions {
  display: flex;
  justify-content: center;
  gap: 16px;
  flex-wrap: wrap;
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

@media (max-width: 600px) {
  .summary-card {
    padding: 28px;
  }
}
</style>
