<script setup>
  import { onMounted, ref } from 'vue';
  import { RouterLink, useRoute } from 'vue-router';
  const pet = ref({});
  const tutores = ref([]);

  const route = useRoute();
  const API_URL = 'http://localhost:3000';


  async function carregarPet() {
    const idPet = route.params.id;
    console.log('Id do Pet:', idPet);


  const respostaPet = await fetch(`${API_URL}/pets/${idPet}`);
  pet.value = await respostaPet.json();

  const respostaTutor = await fetch(`${API_URL}/tutores`);
  tutores.value = await respostaTutor.json();
  }

  onMounted(carregarPet);
</script>

<template>
  <h1> Nome do Pet: {{ pet.nome }}</h1>
  <p> Espécie: {{ pet.especie }}</p>
  <p> Tutor: {{ tutores.find(tutor => tutor.id === pet.tutorId)?.nome }}</p>

  <button class="btn btn-primary">
    <RouterLink :to="{ name: 'pets' }">Voltar</RouterLink>
  </button>

</template>
