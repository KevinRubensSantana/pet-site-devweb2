<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRoute } from 'vue-router';
const pet = ref({});
const route = useRoute();
const API_URL = 'http://localhost:5173/';
const tutor = ref({});

async function carregarPet () {
  const idPet = route.params.id;
  console.log(`ID do pet:`, idPet);

  const respostaPet = await fetch(`${API_URL}/pets/${idPet}`);

  pet.value = await respostaPet.json();

  const respostaTutor = await fetch(
    `${API_URL}/tutores/${pet.value.tutorId}`
  );
  tutor.value = await respostaTutor.json();
}
onMounted(carregarPet);
</script>

<template>
  <h1>Nome do Pet: {{ pet.nome }}</h1>
  <p>Especie: {{ pet.especie }}</p>

  <button class="btn btn-primary">
    <RouterLink :to="{ name: 'pets' }">
      Voltar
    </RouterLink>
  </button>
</template>

<style scoped>
</style>

