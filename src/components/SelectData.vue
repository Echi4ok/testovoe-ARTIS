<script setup>
import { ref, watch } from 'vue';
import axios from 'axios';

// Определяем две переменные для хранения значений дат
const from_date = ref(null); // начальная дата
const to_date = ref(null); // конечная дата
const isShow = ref(false); // булевая переменная, отвечающая за отображение блока с информацией
const items = ref([]); // массив с объектами, где хранятся данные
const errorMessage = ref(''); // сообщение об ошибке



// запрос к серверу с передачей параметров начальных и конечных дат
watch([from_date, to_date], () => {
  // проверяем, что обе даты выбраны
  if (from_date.value, to_date.value) {
    axios.get(`https://bot.artis21.ru/api/get_surveys.php`, {
        params: {
          from_date: from_date.value,
          to_date: to_date.value
        } 
    })
      .then((res) => {
        let response = res.data;
        console.log(response);
        if (response.length) {
            isShow.value = true;
            items.value = response; // заполняем массив данными из ответа
        } else {
            isShow.value = false; // если нет данных, скрываем блок
            errorMessage.value = 'Нет данных за указанный период.'; // сообщение об отсутствии данных :(
        }
      })
      .catch((err) => {
        console.error(err);
        errorMessage.value = 'Ошибка при получении данных с сервера. Попробуйте позже.'; // Сообщение об ошибке, капут(
      });
  } else {
    errorMessage.value = 'Некорректный диапазон дат. Убедитесь, что начальная дата меньше или равна конечной.'; // капут :(
}});
</script>

<template>
  <div class="block">
    <div class="block_one">
      <v-date-picker v-model="from_date" label="Start Date" class="rounded" :max="to_date"></v-date-picker>
    </div>
    <div class="block_two">
      <v-date-picker v-model="to_date" label="End Date" class="rounded" :min="from_date"></v-date-picker>
    </div>
  </div>
  
  <!-- Блок с информацией, отображается если есть данные -->
  <div v-if="isShow" class="info_block">
    <ul>
      <li v-for="item in items" :key="item.id" class="item">
        <div class="item_content">
            <h2>{{ item.assemblers_rating }}</h2>
            <p>{{ item.comment }}</p>
            <p>{{ item.consultant_rating }}</p>
            <p>{{ item.dateTime }}</p>
            <p>{{ item.dispatcher_rating }}</p>
            <p>{{ item.id }}</p>
            <p>{{ item.order_id }}</p>
            <p>{{ item.product_rating }}</p>
            <p>{{ item.reclamation }}</p>
            <p>{{ item.recommend_rating }}</p>
            <p>{{ item.source }}</p>
            <p>{{ item.status }}</p>
            <hr>
        </div>
      </li>
    </ul>
  </div>

  <!-- Капут -->
  <div v-if="errorMessage" class="error-message">
    <h3>{{ errorMessage }}</h3>
  </div>
</template>

<style scoped>
.block {
  display: flex;
  justify-content: center; 
  align-items: flex-end;
}
.block_one, .block_two {
  padding: 15px;
}
.info_block {
  background-color: #007BFF; 
  border-radius: 8px; 
  padding: 20px; 
  margin: 20px; 
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); 
}
.item:hover {
  transform: scale(1.02); 
}
.item_content {
  display: flex;
  flex-direction: column; 
}
li {
    list-style: none;
}
.error-message {
  color: red; /* Цвет текста для сообщения об ошибке */
  text-align: center; /* Центрируем текст ошибки */
  margin-top: 20px; /* Отступ сверху */
}
</style>
