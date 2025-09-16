<template>
  <div class="setup-card">
    <h2>Choose your challenge</h2>
    <p>Select how many questions you'd like, pick a difficulty, and optionally focus on a category.</p>

    <form class="setup-form" @submit.prevent="submit">
      <label>
        Number of questions
        <select v-model.number="form.amount">
          <option v-for="option in amountOptions" :key="option" :value="option">
            {{ option }}
          </option>
        </select>
      </label>

      <label>
        Difficulty
        <select v-model="form.difficulty">
          <option value="">Any difficulty</option>
          <option value="easy">Easy</option>
          <option value="medium">Medium</option>
          <option value="hard">Hard</option>
        </select>
      </label>

      <label>
        Category
        <select v-model="form.category">
          <option value="">Any category</option>
          <option
            v-for="category in categories"
            :key="category.id"
            :value="category.id"
          >
            {{ category.name }}
          </option>
        </select>
      </label>

      <div v-if="categoryError" class="category-error">
        {{ categoryError }}
      </div>

      <button
        type="submit"
        class="primary"
        :disabled="loadingCategories && !categories.length"
      >
        {{ loadingCategories && !categories.length ? 'Loading categories…' : 'Start quiz' }}
      </button>
    </form>
  </div>
</template>

<script setup>
import { onMounted, reactive, ref } from 'vue';
import axios from 'axios';

const emit = defineEmits(['start']);

const amountOptions = [5, 10, 15, 20];
const categories = ref([]);
const categoryError = ref('');
const loadingCategories = ref(false);

const form = reactive({
  amount: 10,
  difficulty: '',
  category: ''
});

onMounted(async () => {
  loadingCategories.value = true;
  categoryError.value = '';

  try {
    const { data } = await axios.get('https://opentdb.com/api_category.php');
    categories.value = data.trivia_categories || [];
  } catch (error) {
    categoryError.value = 'Unable to load categories right now. You can still play without selecting one.';
  } finally {
    loadingCategories.value = false;
  }
});

const submit = () => {
  const payload = {
    amount: form.amount,
    difficulty: form.difficulty,
    category: form.category,
    categoryName: resolveCategoryName(form.category)
  };

  emit('start', payload);
};

const resolveCategoryName = id => {
  if (!id) return 'Any category';
  const match = categories.value.find(category => String(category.id) === String(id));
  return match ? match.name : 'Selected category';
};
</script>

<style scoped>
.setup-card {
  background: white;
  border-radius: 24px;
  padding: 32px;
  box-shadow: 0 30px 60px -40px rgba(15, 23, 42, 0.45);
  max-width: 520px;
  width: 100%;
  margin: 0 auto;
  text-align: left;
}

h2 {
  margin-top: 0;
  margin-bottom: 8px;
  font-size: 28px;
  font-weight: 700;
}

p {
  margin-top: 0;
  margin-bottom: 24px;
  color: #475569;
}

.setup-form {
  display: grid;
  gap: 16px;
}

label {
  display: grid;
  gap: 8px;
  font-weight: 600;
  color: #0f172a;
}

select {
  appearance: none;
  border: 1px solid #cbd5f5;
  border-radius: 12px;
  padding: 12px 16px;
  font-size: 16px;
  background: #f8fafc;
  color: inherit;
  transition: border 0.2s ease, box-shadow 0.2s ease;
}

select:focus {
  border-color: #2563eb;
  box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.1);
  outline: none;
}

.primary {
  margin-top: 8px;
  padding: 14px 18px;
  border-radius: 999px;
  background: linear-gradient(135deg, #2563eb, #9333ea);
  color: white;
  font-weight: 600;
  letter-spacing: 0.02em;
  border: none;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 18px 30px -22px rgba(37, 99, 235, 0.9);
}

.primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  box-shadow: none;
}

.category-error {
  background: rgba(239, 68, 68, 0.1);
  border-radius: 12px;
  padding: 12px 14px;
  color: #b91c1c;
  font-size: 14px;
}
</style>
