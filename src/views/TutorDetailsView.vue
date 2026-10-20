<script setup>
import { ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import { RouterLink } from 'vue-router';

const route = useRoute();
const API_URL = 'http://localhost:3000';
const tutor = ref({});

async function carregarTutor() {
  const idTutor = route.params.id;
  const resposta = await fetch(`${API_URL}/tutores/${idTutor}`);
  tutor.value = await resposta.json();
}

watch(
  () => route.params.id,
  () => {
    carregarTutor();
  },
  { immediate: true },
);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Detalhes do Tutor</h1>
      <p class="text-body-secondary mb-0">Visualização do tutor selecionado.</p>
    </header>

    <div class="card shadow-sm">
      <div class="card-body">
        <p><strong>ID:</strong> {{ tutor.id }}</p>
        <p><strong>Nome:</strong> {{ tutor.nome }}</p>
        <p><strong>Telefone:</strong> {{ tutor.telefone }}</p>
      </div>
    </div>

    <div class="mt-3">
      <RouterLink
        class="btn btn-secondary"
        :to="{ name: 'tutores' }"
      >
        Voltar para a listagem
      </RouterLink>
    </div>
  </div>
</template>
