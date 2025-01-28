<template>
  <div class="question-card">
    <p>{{ question.text }}</p>
    <button :class="{ answered: question.userAnswered === true }" @click="answer(true)">Да</button>
    <button :class="{ answered: question.userAnswered === false }" @click="answer(false)">Нет</button>
    <input type="checkbox" :disabled="!question.doublePoints && doublePointsCount >= 3" v-model="question.doublePoints" @change="updateDoublePointsCount" /> Удвоить
  </div>
</template>

<script setup>
const props = defineProps({
  question: Object,
  index: Number,
  doublePointsCount: Number
});

const emit = defineEmits(["answer", "update-double-points"]);

const answer = (userAnswer) => {
  emit("answer", { index: props.index, userAnswer });
  props.question.userAnswered = userAnswer; // Установка статуса ответа
};

const updateDoublePointsCount = () => {
  emit("update-double-points");
};
</script>

<style scoped>
.question-card {
  border: 1px solid #0e0404;
  padding: 10px;
  margin: 10px;
  border-radius: 5px;
}
button {
  margin: 5px;
  padding: 10px 20px;
  cursor: pointer;
  border: none;
  border-radius: 5px;
  background-color: #42b983;
  color: white;
  transition: all 0.3s ease;
}
button:hover {
  background-color: #2a8b68;
}
button.answered {
  background-color: #ffcc00;
  border: 2px solid #ffaa00;
}
</style>