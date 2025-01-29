<template>
  <div id="app">
    <Modal v-if="showRules" :show="showRules" @close="closeRules" />
    <h1>БЛЕФЫ</h1>
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
      <button v-if="allAnswered" @click="submitAnswers">Отправить ответы</button>
    </div>

    <div v-else>
      <h2>Результат</h2>
      <p>Правильных ответов: {{ correctAnswersCount }} из 10</p>
      <div>
        <h3>Правильные ответы:</h3>
        <ul>
          <li v-for="(answer, index) in currentQuestions" :key="index">
            Вопрос: {{ answer.text }} -  Ответ: {{ answer.correctAnswer ? 'Да' : 'Нет' }}
          </li>
        </ul>
        
      </div>
      <button v-if="!quizFinished" @click="startNewQuiz">Следующая карточка</button>
      <div v-else>
      <h2>Поздравляем! Вы ответили на все вопросы!</h2>
      <p>Общее количество очков: <span>{{ totalScore }}</span></p>
      <button  @click="restartQuiz">Пройти заново</button>
    </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { questions } from "./questions";
import Question from "./components/Question.vue";
import Modal from "./components/Modal.vue";

const showRules = ref(false);
const currentQuestions = ref([]);
const userAnswers = ref([]);
const quizComplete = ref(false);
const correctAnswersCount = ref(0);
const doublePointsCount = ref(0);
const currentIndex = ref(0);
const totalScore = ref(0);
const quizFinished = ref(false);

onMounted(() => {
  if (!localStorage.getItem("rulesShown")) {
    showRules.value = true;
  }
  generateOrderedQuestions();
});

const closeRules = () => {
  showRules.value = false;
  localStorage.setItem("rulesShown", "true");
};

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
  correctAnswersCount.value = userAnswers.value.filter((answer, index) => 
    answer === currentQuestions.value[index].correctAnswer
  ).length;

  const roundScore = userAnswers.value.reduce((total, answer, index) => {
    const isCorrect = answer === currentQuestions.value[index].correctAnswer;
    const points = isCorrect ? (currentQuestions.value[index].doublePoints ? 2 : 1) : 0;
    return total + points;
  }, 0);

  totalScore.value += roundScore;

  if (currentIndex.value + 7 >= questions.length) {
    quizFinished.value = true;
    localStorage.removeItem("totalScore");
  }
  quizComplete.value = true;
};

const startNewQuiz = () => {
  currentIndex.value += 7;
  generateOrderedQuestions();
};

const restartQuiz = () => {
  currentIndex.value = 0;
  totalScore.value = 0;
  localStorage.removeItem("totalScore");
  localStorage.removeItem("rulesShown");
  quizFinished.value = false;
  generateOrderedQuestions();
};

const allAnswered = computed(() => userAnswers.value.every((answer) => answer !== null));
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 20px;
  width: 80%;
  background-color: lightgray;
  border-color: gray;
  margin: auto;
  padding: 10px
  ;
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
.next {
  margin-bottom: 10px;
}
li {
  text-align: left;
  margin-top: 5px;
}
p span {
  font-weight: bold;
}
</style>

