<template>
    <div v-if="show" class="modal-overlay">
      <div class="modal">
        <h2>Правила</h2>
        <p>Отвечайте на вопросы "Да" или "Нет".</p>
        <P>Можно удвоить баллы за три вопроса. </P>
        <P>На неправильные ответы удвоение не влияет</P>
        <button @click="$emit('close')">Понятно</button>
      </div>
    </div>
  </template>
  
  <script setup>
  const props = defineProps({ show: Boolean });
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .modal {
    background: white;
    padding: 20px;
    border-radius: 5px;
    text-align: center;
  }
  </style>