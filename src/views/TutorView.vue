<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';
const tutores = ref([]);

async function carregarTutores() {
  const resposta = await fetch(`${API_URL}/tutores`);
  tutores.value = await resposta.json();
}

async function excluirTutor(id) {
  const confirmarExclusao = window.confirm(
    'Tem certeza que deseja excluir este tutor?',
  );

  if (!confirmarExclusao) {
    return;
  }

  await fetch(`${API_URL}/tutores/${id}`, {
    method: 'DELETE',
  });

  await carregarTutores();
}

onMounted(carregarTutores);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Tutores</h1>
      <p class="text-body-secondary mb-0">
        Listagem dos tutores cadastrados no sistema.
      </p>
    </header>

    <div class="d-flex justify-content-between align-items-center mb-3">
      <RouterLink
        class="btn btn-primary"
        :to="{ name: 'addTutor' }"
      >
        Cadastrar Tutor
      </RouterLink>
    </div>

    <table class="table table-striped table-hover align-middle">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome</th>
          <th>Telefone</th>
          <th class="text-center">Ações</th>
        </tr>
      </thead>

      <tbody>
        <tr
          v-for="tutor in tutores"
          :key="tutor.id"
        >
          <td>{{ tutor.id }}</td>
          <td>{{ tutor.nome }}</td>
          <td>{{ tutor.telefone }}</td>
          <td class="text-center">
            <div class="d-flex justify-content-center gap-2">
              <RouterLink
                class="btn btn-sm btn-outline-primary"
                :to="`/tutores/${tutor.id}`"
              >
                Visualizar
              </RouterLink>
              <button
                class="btn btn-sm btn-danger"
                type="button"
                @click="excluirTutor(tutor.id)"
              >
                Excluir
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
