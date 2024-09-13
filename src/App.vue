<template>
  <div class="container mt-5">
    <h1 class="text-center">Consulta YesNo</h1>
    <div class="chat-container">
      <div
        v-for="(mensaje, index) in mensajes"
        :key="index"
        :class="{'message': true, 'user': mensaje.tipo === 'usuario', 'bot': mensaje.tipo === 'bot'}"
      >
        <div v-if="mensaje.tipo === 'bot'">
          <p>{{ mensaje.texto }}</p>
          <img
            v-if="mensaje.imagen"
            :src="mensaje.imagen"
            alt="Respuesta"
            class="img-fluid"
          />
        </div>
        <div v-if="mensaje.tipo === 'usuario'">
          <p>{{ mensaje.texto }}</p>
        </div>
      </div>
    </div>
    <div class="input-group mt-3">
      <input
        v-model="pregunta"
        type="text"
        class="form-control"
        placeholder="Escribe tu pregunta..."
      />
      <button @click="consultarApi" class="btn btn-primary">Consultar</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const pregunta = ref('');
const mensajes = ref([]);

const consultarApi = async () => {
  if (!pregunta.value) {
    return;
  }

  // Agregar la pregunta del usuario y limpiar los mensajes anteriores
  mensajes.value = [{ tipo: 'usuario', texto: pregunta.value }];

  pregunta.value = '';

  try {
    const response = await axios.get('https://yesno.wtf/api');
    // Agregar la respuesta del bot
    mensajes.value.push({
      tipo: 'bot',
      texto: response.data.answer,
      imagen: response.data.image,
    });
  } catch (error) {
    mensajes.value.push({
      tipo: 'bot',
      texto: 'No se pudo obtener la respuesta. Intenta de nuevo más tarde.',
    });
  }
};
</script>

<style scoped>
/* Custom Styles */
body {
  font-family: Arial, sans-serif;
  background-color: #f8f9fa;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 600px;
  margin: 20px auto;
  padding: 15px;
  background: #ffffff;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h1 {
  font-size: 1.5rem;
  text-align: center;
  color: #007bff;
  margin-bottom: 15px;
}

.chat-container {
  border: 1px solid #ddd;
  padding: 10px;
  height: 300px;
  overflow-y: auto;
  background: #f9f9f9;
  border-radius: 5px;
  margin-bottom: 15px;
}

.message {
  padding: 8px;
  margin-bottom: 10px;
}

.message.user {
  text-align: right;
  background-color: #e0f7fa;
  border-radius: 10px;
}

.message.bot {
  text-align: left;
  background-color: #f1f8e9;
  border-radius: 10px;
}

.message img {
  max-width: 100%;
  border-radius: 5px;
}

.input-group {
  display: flex;
  align-items: center;
}

.form-control {
  flex: 1;
  margin-right: 10px;
  border-radius: 5px;
  border: 1px solid #ced4da;
}

.btn-primary {
  background-color: #007bff;
  border: none;
  color: #fff;
  padding: 10px 15px;
  border-radius: 5px;
}
</style>
