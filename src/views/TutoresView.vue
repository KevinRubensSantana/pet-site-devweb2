<script setup>
import { computed, onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';
const tutores = ref([]);
const pets = ref([]);
const termoBusca = ref('');

async function carregarDados() {
  const [respostaTutores, respostaPets] = await Promise.all([
    fetch(`${API_URL}/tutores`),
    fetch(`${API_URL}/pets`),
  ]);

  tutores.value = await respostaTutores.json();
  pets.value = await respostaPets.json();
}

function petsDoTutor(tutorId) {
  return pets.value.filter((pet) => String(pet.tutorId) === String(tutorId));
}

const tutoresFiltrados = computed(() => {
  const texto = termoBusca.value.trim().toLowerCase();

  if (!texto) {
    return tutores.value;
  }

  return tutores.value.filter((tutor) => {
    const nome = (tutor.nome || '').toLowerCase();
    const cpf = (tutor.cpfCnpj || '').toLowerCase();
    const email = (tutor.email || '').toLowerCase();
    const telefone = (tutor.telefone || '').toLowerCase();

    return nome.includes(texto) || cpf.includes(texto) || email.includes(texto) || telefone.includes(texto);
  });
});

async function excluirTutor(id) {
  const tutorComPets = petsDoTutor(id);

  if (tutorComPets.length > 0) {
    alert('Este tutor possui pets vinculados. Remova ou transfira os pets antes de excluir o tutor.');
    return;
  }

  const confirmarExclusao = window.confirm('Deseja realmente remover este tutor?');

  if (!confirmarExclusao) {
    return;
  }

  await fetch(`${API_URL}/tutores/${id}`, {
    method: 'DELETE',
  });

  await carregarDados();
}

onMounted(carregarDados);
</script>

<template>
  <div class="page-shell">
    <header class="page-header">
      <div>
        <h1>Listagem de Tutores</h1>
        <p>Consulte os tutores cadastrados e seus pets.</p>
      </div>

      <RouterLink class="btn btn-primary add-btn" :to="{ name: 'addTutor' }">
        + Cadastrar Tutor
      </RouterLink>
    </header>

    <div class="search-box">
      <span class="search-icon">⌕</span>
      <input
        v-model="termoBusca"
        type="text"
        placeholder="Pesquisar tutor por nome, CPF/CNPJ ou e-mail"
      />
    </div>

    <div class="table-wrap">
      <table class="table table-hover align-middle">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nome Completo</th>
            <th>CPF / CNPJ</th>
            <th>Telefone / Celular / WhatsApp</th>
            <th>E-mail</th>
            <th>Pets (Quantidade)</th>
            <th>Nomes dos Pets</th>
            <th>Ações</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="tutor in tutoresFiltrados" :key="tutor.id">
            <td>{{ tutor.id }}</td>
            <td>{{ tutor.nome }}</td>
            <td>{{ tutor.cpfCnpj || '---' }}</td>
            <td>{{ tutor.telefone || '---' }}</td>
            <td>{{ tutor.email || '---' }}</td>
            <td>{{ petsDoTutor(tutor.id).length }}</td>
            <td>
              <span v-if="petsDoTutor(tutor.id).length > 0">
                {{ petsDoTutor(tutor.id).map((pet) => pet.nome).join(' / ') }}
              </span>
              <span v-else class="text-body-secondary">Nenhum pet vinculado</span>
            </td>
            <td>
              <div class="action-group">
                <RouterLink class="btn btn-sm btn-outline-primary action-btn" :to="`/tutores/${tutor.id}`">
                  Visualizar
                </RouterLink>
                <button class="btn btn-sm btn-outline-danger action-btn danger-btn" @click="excluirTutor(tutor.id)">
                  Remover
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="footer-text">Exibindo {{ tutoresFiltrados.length }} tutores</div>
  </div>
</template>

<style scoped>
.page-shell {
  width: 100%;
}

.page-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.page-header h1 {
  margin: 0;
  font-size: clamp(2.2rem, 3vw, 3rem);
  font-weight: 800;
  color: #1d2733;
}

.page-header p {
  margin: 0.4rem 0 0;
  color: #5f6b7a;
  font-size: 1.1rem;
}

.add-btn {
  min-width: 190px;
  min-height: 48px;
  border-radius: 8px;
  font-weight: 700;
  font-size: 1.05rem;
}

.search-box {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  width: 100%;
  background: #fff;
  border: 1px solid #dfe4ea;
  border-radius: 10px;
  padding: 0.9rem 1rem;
  margin-bottom: 1.3rem;
}

.search-icon {
  font-size: 1.5rem;
  color: #6c757d;
  line-height: 1;
}

.search-box input {
  width: 100%;
  border: none;
  outline: none;
  font-size: 1.05rem;
  color: #212529;
  background: transparent;
}

.search-box input::placeholder {
  color: #7a8594;
}

.table-wrap {
  width: 100%;
  overflow-x: auto;
  background: #fff;
  border: 1px solid #dde3ea;
  border-radius: 10px;
  overflow: hidden;
}

.table {
  margin: 0;
  min-width: 1200px;
}

.table thead th {
  background: #edf0f3;
  color: #1e2a37;
  font-weight: 700;
  border-bottom: 1px solid #dde3ea;
  padding: 0.9rem 0.8rem;
  vertical-align: middle;
}

.table tbody td {
  padding: 0.9rem 0.8rem;
  vertical-align: middle;
  color: #1f2937;
}

.action-group {
  display: flex;
  gap: 0.6rem;
  align-items: center;
  flex-wrap: wrap;
}

.action-btn {
  min-width: 100px;
  border-radius: 7px;
  font-weight: 600;
}

.danger-btn {
  border-color: #dc3545;
  color: #dc3545;
}

.footer-text {
  margin-top: 1rem;
  color: #5f6b7a;
  font-size: 1rem;
}

@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    align-items: stretch;
  }

  .add-btn {
    width: 100%;
  }
}
</style>
