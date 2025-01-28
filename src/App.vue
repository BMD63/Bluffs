<template>
  <div id="app">
    <h1>Викторина</h1>
    <div v-if="!quizComplete">
      <Question
        v-for="(question, index) in currentQuestions"
        :key="index"
        :question="question"
        :index="index"
        :doublePointsCount="doublePointsCount"
        @answer="handleAnswer"
        @update-double-points="updateDoublePointsCount"
      />
      <button v-if="allAnswered" @click="submitAnswers">Ответить</button>
    </div>

    <div v-else>
      <h2>Результаты</h2>
      <p>Очки: {{ correctAnswersCount }}</p>
      <div>
        <h3>Правильные ответы:</h3>
        <ul>
          <li v-for="(answer, index) in currentQuestions" :key="index">
            Вопрос: {{ answer.text }} - {{ answer.correctAnswer ? 'Да' : 'Нет' }}
          </li>
        </ul>
      </div>
      <button @click="startNewQuiz">Следующая карточка</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { questions } from "./questions";
import Question from "./Question.vue";

const currentQuestions = ref([]);
const userAnswers = ref([]);
const quizComplete = ref(false);
const correctAnswersCount = ref(0);
const doublePointsCount = ref(0);
const currentIndex = ref(0);

const generateOrderedQuestions = () => {
  currentQuestions.value = questions.slice(currentIndex.value, currentIndex.value + 7).map(q => ({ ...q, doublePoints: false, userAnswered: null }));
  userAnswers.value = Array(currentQuestions.value.length).fill(null);
  quizComplete.value = false;
  doublePointsCount.value = 0;
};

const updateDoublePointsCount = () => {
  doublePointsCount.value = currentQuestions.value.filter(q => q.doublePoints).length;
};

const handleAnswer = ({ index, userAnswer }) => {
  userAnswers.value[index] = userAnswer;
  currentQuestions.value[index].userAnswered = userAnswer;
};

const submitAnswers = () => {
  correctAnswersCount.value = userAnswers.value.reduce((total, answer, index) => {
    const isCorrect = answer === currentQuestions.value[index].correctAnswer;
    const points = isCorrect ? (currentQuestions.value[index].doublePoints ? 2 : 1) : 0;
    return total + points;
  }, 0);
  quizComplete.value = true;
};

const startNewQuiz = () => {
  currentIndex.value += 7;
  if (currentIndex.value >= questions.length) {
    currentIndex.value = 0;
  }
  generateOrderedQuestions();
};

const allAnswered = computed(() => userAnswers.value.every((answer) => answer !== null));

generateOrderedQuestions();
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 20px;
}
button {
  margin: 5px;
  padding: 10px 20px;
  cursor: pointer;
  border: none;
  border-radius: 5px;
  background-color: #42b983;
  color: white;
}
button:hover {
  background-color: #2a8b68;
}
</style>

