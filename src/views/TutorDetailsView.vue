<script setup>
import { onMounted, ref, watch } from 'vue';
import { RouterLink, useRoute } from 'vue-router';

const API_URL = 'http://localhost:3000';
const route = useRoute();

const tutor = ref({});
const pets = ref([]);
const novoPet = ref({
  nome: '',
  especie: '',
});

const errosPet = ref({
  nome: '',
  especie: '',
});

async function carregarTutor() {
  const tutorId = route.params.id;
  const resposta = await fetch(`${API_URL}/tutores/${tutorId}`);
  tutor.value = await resposta.json();
}

async function carregarPetsDoTutor() {
  const tutorId = route.params.id;
  const resposta = await fetch(`${API_URL}/pets`);
  const todosPets = await resposta.json();

  pets.value = todosPets.filter((pet) => String(pet.tutorId) === String(tutorId));
}

async function carregarDados() {
  if (!route.params.id) {
    return;
  }

  await carregarTutor();
  await carregarPetsDoTutor();
}

function validarPet() {
  let valido = true;

  errosPet.value.nome = '';
  errosPet.value.especie = '';

  if (!novoPet.value.nome.trim()) {
    errosPet.value.nome = 'Ops, campo obrigatório, por favor preencha-o!';
    valido = false;
  }

  if (!novoPet.value.especie.trim()) {
    errosPet.value.especie = 'Ops, campo obrigatório, por favor preencha-o!';
    valido = false;
  }

  return valido;
}

async function salvarPet() {
  if (!validarPet()) {
    return;
  }

  const resposta = await fetch(`${API_URL}/pets`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      nome: novoPet.value.nome.trim(),
      especie: novoPet.value.especie.trim(),
      tutorId: String(route.params.id),
    }),
  });

  if (!resposta.ok) {
    alert('Não foi possível cadastrar o pet para este tutor.');
    return;
  }

  novoPet.value = { nome: '', especie: '' };
  errosPet.value = { nome: '', especie: '' };

  await carregarPetsDoTutor();
}

watch(
  () => route.params.id,
  async () => {
    await carregarDados();
  },
  { immediate: true },
);

onMounted(carregarDados);
</script>

<template>
  <div class="page-shell">
    <header class="page-header">
      <div class="page-title-wrap">
        <h1>Detalhes do Tutor</h1>
        <p>Visualize os dados do tutor e cadastre pets vinculados.</p>
      </div>

      <RouterLink class="btn btn-outline-primary back-button" :to="{ name: 'tutores' }">
        ← Voltar para Tutores
      </RouterLink>
    </header>

    <section class="detail-section">
      <h2>Informações do Tutor</h2>

      <div class="data-card">
        <div class="card-header">Dados do Tutor</div>

        <div class="info-grid info-grid-3">
          <div class="info-item">
            <label>ID</label>
            <span>{{ tutor.id || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>Nome Completo</label>
            <span>{{ tutor.nome || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>CPF / CNPJ</label>
            <span>{{ tutor.cpfCnpj || '••••••••••' }}</span>
          </div>

          <div class="info-item">
            <label>Telefone / Celular / WhatsApp</label>
            <span>{{ tutor.telefone || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>E-mail</label>
            <span>{{ tutor.email || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>Data de Nascimento</label>
            <span>{{ tutor.dataNascimento || 'Não informado' }}</span>
          </div>
        </div>
      </div>

      <div class="data-card mt-4">
        <div class="card-header">Endereço</div>

        <div class="info-grid info-grid-3">
          <div class="info-item">
            <label>Rua</label>
            <span>{{ tutor.rua || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>Número</label>
            <span>{{ tutor.numero || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>Bairro</label>
            <span>{{ tutor.bairro || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>Cidade</label>
            <span>{{ tutor.cidade || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>Estado</label>
            <span>{{ tutor.estado || 'Não informado' }}</span>
          </div>

          <div class="info-item">
            <label>CEP</label>
            <span>{{ tutor.cep || 'Não informado' }}</span>
          </div>
        </div>
      </div>
    </section>

    <section class="pets-section">
      <h2>Pets do Tutor</h2>

      <div class="pets-layout">
        <div class="table-panel">
          <div class="panel-header">
            <span class="panel-label">Pets vinculados</span>
            <span class="badge-count">{{ pets.length }}</span>
          </div>

          <table class="table table-hover align-middle mb-0">
            <thead>
              <tr>
                <th>ID</th>
                <th>Nome</th>
                <th>Espécie</th>
                <th>Ações</th>
              </tr>
            </thead>

            <tbody v-if="pets.length > 0">
              <tr v-for="pet in pets" :key="pet.id">
                <td>{{ pet.id }}</td>
                <td>{{ pet.nome }}</td>
                <td>{{ pet.especie }}</td>
                <td>
                  <RouterLink class="btn btn-sm btn-outline-primary action-btn" :to="`/pets/${pet.id}`">
                    Visualizar
                  </RouterLink>
                </td>
              </tr>
            </tbody>

            <tbody v-else>
              <tr>
                <td colspan="4" class="text-center text-body-secondary py-4">
                  Nenhum pet vinculado a este tutor.
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div class="form-panel">
          <h3>Cadastrar novo pet</h3>
          <p>O pet será vinculado automaticamente a {{ tutor.nome || 'este tutor' }}.</p>

          <form @submit.prevent="salvarPet" class="row g-3">
            <div class="col-12">
              <label for="petNome" class="form-label">Nome do pet</label>
              <input
                id="petNome"
                v-model="novoPet.nome"
                type="text"
                class="form-control"
                :class="{ 'is-invalid': errosPet.nome }"
                placeholder="Digite o nome do pet"
              />
              <div v-if="errosPet.nome" class="text-danger mt-2 small">
                {{ errosPet.nome }}
              </div>
            </div>

            <div class="col-12">
              <label for="petEspecie" class="form-label">Espécie</label>
              <select
                id="petEspecie"
                v-model="novoPet.especie"
                class="form-select"
                :class="{ 'is-invalid': errosPet.especie }"
              >
                <option value="">Selecione</option>
                <option value="Cachorro">Cachorro</option>
                <option value="Gato">Gato</option>
              </select>
              <div v-if="errosPet.especie" class="text-danger mt-2 small">
                {{ errosPet.especie }}
              </div>
            </div>

            <div class="col-12 mt-3">
              <button type="submit" class="btn btn-success w-100 submit-pet-btn">
                Cadastrar pet
              </button>
            </div>
          </form>
        </div>
      </div>
    </section>
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
  gap: 1.5rem;
  margin-bottom: 1.5rem;
}

.page-title-wrap h1 {
  margin: 0;
  font-size: clamp(2.1rem, 3vw, 3rem);
  font-weight: 800;
  color: #1f2937;
}

.page-title-wrap p {
  margin: 0.5rem 0 0;
  color: #5f6b7a;
  font-size: 1.05rem;
}

.back-button {
  min-width: 190px;
  border-radius: 8px;
  border-width: 1.5px;
  font-weight: 600;
  background: transparent;
  color: #0d6efd;
}

.detail-section,
.pets-section {
  background: #f3f4f6;
  border: 1px solid #dde3ea;
  border-radius: 12px;
  padding: 1.15rem 1.25rem 1.4rem;
}

.detail-section h2,
.pets-section h2 {
  margin: 0 0 1rem;
  font-weight: 800;
  color: #1d2733;
  font-size: 2rem;
}

.data-card {
  border: 1px solid #dde3ea;
  border-radius: 10px;
  background: #fff;
  overflow: hidden;
}

.card-header {
  background: #f2f3f5;
  border-bottom: 1px solid #dde3ea;
  font-weight: 700;
  font-size: 1.05rem;
  color: #1d2733;
  padding: 0.85rem 1rem;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1rem;
  padding: 1rem;
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
  min-height: 60px;
}

.info-item label {
  font-size: 0.8rem;
  font-weight: 700;
  color: #495057;
  text-transform: uppercase;
}

.info-item span {
  font-size: 1rem;
  color: #212529;
  font-weight: 500;
}

.pets-layout {
  display: grid;
  grid-template-columns: 1.6fr 0.9fr;
  gap: 1.5rem;
}

.table-panel,
.form-panel {
  background: #fff;
  border: 1px solid #dde3ea;
  border-radius: 10px;
  padding: 1rem;
}

.panel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  margin-bottom: 0.8rem;
}

.panel-label {
  font-weight: 700;
  font-size: 1.1rem;
  color: #1e2a37;
}

.badge-count {
  min-width: 2rem;
  height: 2rem;
  border-radius: 999px;
  background: #0d6efd;
  color: white;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 0.9rem;
}

.table th {
  font-size: 0.8rem;
  text-transform: uppercase;
  letter-spacing: 0.02em;
  color: #495057;
}

.action-btn {
  min-width: 90px;
}

.form-panel h3 {
  font-size: 1.05rem;
  font-weight: 700;
  margin: 0 0 0.4rem;
  color: #1d2733;
}

.form-panel p {
  color: #5f6b7a;
  margin: 0 0 1rem;
  font-size: 0.95rem;
}

.form-label {
  font-weight: 600;
  color: #1d2733;
}

.form-control,
.form-select {
  min-height: 46px;
  border-radius: 0.45rem;
}

.submit-pet-btn {
  min-height: 46px;
  font-weight: 700;
}

@media (max-width: 992px) {
  .page-header {
    flex-direction: column;
    align-items: stretch;
  }

  .back-button {
    width: 100%;
  }

  .info-grid,
  .pets-layout {
    grid-template-columns: 1fr;
  }
}
</style>
