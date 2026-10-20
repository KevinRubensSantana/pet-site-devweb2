<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const router = useRouter();

const novoTutor = ref({
  nome: '',
  telefone: '',
});

async function salvarTutor() {
  await fetch(`${API_URL}/tutores`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(novoTutor.value),
  });

  router.push('/tutores');
}
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Cadastro de Tutor</h1>
      <p class="text-body-secondary mb-0">Cadastre um novo tutor no sistema.</p>
    </header>

    <form
      @submit.prevent="salvarTutor"
      class="row g-3 mt-2"
    >
      <div class="col-md-6">
        <label
          for="nome"
          class="form-label"
          >Nome</label
        >
        <input
          id="nome"
          v-model="novoTutor.nome"
          type="text"
          class="form-control"
          placeholder="Digite o nome do tutor"
          required
        />
      </div>

      <div class="col-md-6">
        <label
          for="telefone"
          class="form-label"
          >Telefone</label
        >
        <input
          id="telefone"
          v-model="novoTutor.telefone"
          type="text"
          class="form-control"
          placeholder="Digite o telefone do tutor"
          required
        />
      </div>

      <div class="col-12">
        <button
          type="submit"
          class="btn btn-primary"
        >
          Salvar
        </button>
      </div>
    </form>
  </div>
</template>
