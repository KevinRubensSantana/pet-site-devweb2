<script setup>
import { computed, ref } from 'vue';
import { useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const router = useRouter();

const novoTutor = ref({
  nome: '',
  cpfCnpj: '',
  telefone: '',
  email: '',
  dataNascimento: '',
  rua: '',
  numero: '',
  bairro: '',
  cidade: '',
  estado: '',
  cep: '',
});

const erros = ref({
  nome: '',
  cpfCnpj: '',
  telefone: '',
  email: '',
  dataNascimento: '',
  rua: '',
  numero: '',
  bairro: '',
  cidade: '',
  estado: '',
  cep: '',
});

const estados = ['AC', 'AL', 'AP', 'AM', 'BA', 'CE', 'DF', 'ES', 'GO', 'MA', 'MT', 'MS', 'MG', 'PA', 'PB', 'PR', 'PE', 'PI', 'RJ', 'RN', 'RS', 'RO', 'RR', 'SC', 'SP', 'SE', 'TO'];

function formatarTelefone(valor) {
  const apenasDigitos = valor.replace(/\D/g, '').slice(0, 11);

  if (apenasDigitos.length <= 2) {
    return apenasDigitos;
  }

  if (apenasDigitos.length <= 7) {
    return `(${apenasDigitos.slice(0, 2)}) ${apenasDigitos.slice(2)}`;
  }

  if (apenasDigitos.length <= 10) {
    return `(${apenasDigitos.slice(0, 2)}) ${apenasDigitos.slice(2, 6)}-${apenasDigitos.slice(6)}`;
  }

  return `(${apenasDigitos.slice(0, 2)}) ${apenasDigitos.slice(2, 7)}-${apenasDigitos.slice(7, 11)}`;
}

function formatarCpfCnpj(valor) {
  const apenasDigitos = valor.replace(/\D/g, '').slice(0, 14);

  if (apenasDigitos.length <= 11) {
    if (apenasDigitos.length <= 3) return apenasDigitos;
    if (apenasDigitos.length <= 6) return `${apenasDigitos.slice(0, 3)}.${apenasDigitos.slice(3)}`;
    if (apenasDigitos.length <= 9) return `${apenasDigitos.slice(0, 3)}.${apenasDigitos.slice(3, 6)}.${apenasDigitos.slice(6)}`;
    return `${apenasDigitos.slice(0, 3)}.${apenasDigitos.slice(3, 6)}.${apenasDigitos.slice(6, 9)}-${apenasDigitos.slice(9, 11)}`;
  }

  if (apenasDigitos.length <= 2) return apenasDigitos;
  if (apenasDigitos.length <= 5) return `${apenasDigitos.slice(0, 2)}.${apenasDigitos.slice(2)}`;
  if (apenasDigitos.length <= 8) return `${apenasDigitos.slice(0, 2)}.${apenasDigitos.slice(2, 5)}.${apenasDigitos.slice(5)}`;
  if (apenasDigitos.length <= 12) return `${apenasDigitos.slice(0, 2)}.${apenasDigitos.slice(2, 5)}.${apenasDigitos.slice(5, 8)}/${apenasDigitos.slice(8)}`;

  return `${apenasDigitos.slice(0, 2)}.${apenasDigitos.slice(2, 5)}.${apenasDigitos.slice(5, 8)}/${apenasDigitos.slice(8, 12)}-${apenasDigitos.slice(12, 14)}`;
}

function formatarCep(valor) {
  const apenasDigitos = valor.replace(/\D/g, '').slice(0, 8);
  if (apenasDigitos.length <= 5) return apenasDigitos;
  return `${apenasDigitos.slice(0, 5)}-${apenasDigitos.slice(5, 8)}`;
}

function validarCampoObrigatorio(campo, mensagem) {
  if (!novoTutor.value[campo].trim()) {
    erros.value[campo] = mensagem;
    return false;
  }

  erros.value[campo] = '';
  return true;
}

function validarNome() {
  return validarCampoObrigatorio('nome', 'Ops, campo obrigatório, por favor preencha-o!');
}

function validarCpfCnpj() {
  if (!novoTutor.value.cpfCnpj.trim()) {
    erros.value.cpfCnpj = 'Ops, campo obrigatório, por favor preencha-o!';
    return false;
  }

  const apenasDigitos = novoTutor.value.cpfCnpj.replace(/\D/g, '');

  if (apenasDigitos.length !== 11 && apenasDigitos.length !== 14) {
    erros.value.cpfCnpj = 'Informe um CPF ou CNPJ válido.';
    return false;
  }

  erros.value.cpfCnpj = '';
  return true;
}

function validarTelefone() {
  if (!novoTutor.value.telefone.trim()) {
    erros.value.telefone = 'Ops, campo obrigatório, por favor preencha-o!';
    return false;
  }

  const apenasDigitos = novoTutor.value.telefone.replace(/\D/g, '');

  if (apenasDigitos.length < 10) {
    erros.value.telefone = 'Informe um telefone válido.';
    return false;
  }

  erros.value.telefone = '';
  return true;
}

function validarEmail() {
  if (!novoTutor.value.email.trim()) {
    erros.value.email = 'Ops, campo obrigatório, por favor preencha-o!';
    return false;
  }

  const emailValido = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(novoTutor.value.email.trim());

  if (!emailValido) {
    erros.value.email = 'Informe um e-mail válido.';
    return false;
  }

  erros.value.email = '';
  return true;
}

function validarDataNascimento() {
  return validarCampoObrigatorio('dataNascimento', 'Ops, campo obrigatório, por favor preencha-o!');
}

function validarCampos() {
  return (
    validarNome() &&
    validarCpfCnpj() &&
    validarTelefone() &&
    validarEmail() &&
    validarDataNascimento() &&
    validarCampoObrigatorio('rua', 'Ops, campo obrigatório, por favor preencha-o!') &&
    validarCampoObrigatorio('numero', 'Ops, campo obrigatório, por favor preencha-o!') &&
    validarCampoObrigatorio('bairro', 'Ops, campo obrigatório, por favor preencha-o!') &&
    validarCampoObrigatorio('cidade', 'Ops, campo obrigatório, por favor preencha-o!') &&
    validarCampoObrigatorio('estado', 'Ops, campo obrigatório, por favor preencha-o!') &&
    validarCampoObrigatorio('cep', 'Ops, campo obrigatório, por favor preencha-o!')
  );
}

const formularioValido = computed(() => {
  return Object.values(novoTutor.value).every((valor) => String(valor).trim().length > 0);
});

async function salvarTutor() {
  const valido = validarCampos();

  if (!valido) {
    return;
  }

  await fetch(`${API_URL}/tutores`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      nome: novoTutor.value.nome.trim(),
      cpfCnpj: novoTutor.value.cpfCnpj.trim(),
      telefone: novoTutor.value.telefone.trim(),
      email: novoTutor.value.email.trim(),
      dataNascimento: novoTutor.value.dataNascimento,
      rua: novoTutor.value.rua.trim(),
      numero: novoTutor.value.numero.trim(),
      bairro: novoTutor.value.bairro.trim(),
      cidade: novoTutor.value.cidade.trim(),
      estado: novoTutor.value.estado.trim(),
      cep: novoTutor.value.cep.trim(),
    }),
  });

  router.push('/tutores');
}
</script>

<template>
  <div class="page-shell">
    <header class="mb-4 page-header">
      <div>
        <h1 class="text-2xl fw-bold mb-1">Cadastrar Tutor</h1>
        <p class="text-body-secondary mb-0">Preencha os dados do tutor.</p>
      </div>
    </header>

    <div class="card border-0 shadow-sm">
      <div class="card-body p-4 p-lg-5">
        <form @submit.prevent="salvarTutor" class="row g-3">
          <div class="col-12 mb-2">
            <h2 class="section-title mb-3">Dados do Tutor</h2>
          </div>

          <div class="col-md-6">
            <label for="idTutor" class="form-label fw-semibold">ID</label>
            <input id="idTutor" type="text" class="form-control" value="Gerado automaticamente" disabled />
          </div>

          <div class="col-md-6">
            <label for="nome" class="form-label fw-semibold">Nome Completo</label>
            <input
              id="nome"
              v-model="novoTutor.nome"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.nome }"
              placeholder="Digite o nome completo"
              @blur="validarNome"
            />
            <div v-if="erros.nome" class="text-danger mt-2 small">{{ erros.nome }}</div>
          </div>

          <div class="col-md-6">
            <label for="cpfCnpj" class="form-label fw-semibold">CPF / CNPJ</label>
            <input
              id="cpfCnpj"
              :value="novoTutor.cpfCnpj"
              @input="novoTutor.cpfCnpj = formatarCpfCnpj($event.target.value)"
              @blur="validarCpfCnpj"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.cpfCnpj }"
              placeholder="123.456.789-00"
            />
            <div v-if="erros.cpfCnpj" class="text-danger mt-2 small">{{ erros.cpfCnpj }}</div>
          </div>

          <div class="col-md-6">
            <label for="telefone" class="form-label fw-semibold">Telefone / Celular / WhatsApp</label>
            <input
              id="telefone"
              :value="novoTutor.telefone"
              @input="novoTutor.telefone = formatarTelefone($event.target.value)"
              @blur="validarTelefone"
              type="tel"
              class="form-control"
              :class="{ 'is-invalid': erros.telefone }"
              placeholder="(00) 00000-0000"
            />
            <div v-if="erros.telefone" class="text-danger mt-2 small">{{ erros.telefone }}</div>
          </div>

          <div class="col-md-6">
            <label for="email" class="form-label fw-semibold">E-mail</label>
            <input
              id="email"
              v-model="novoTutor.email"
              type="email"
              class="form-control"
              :class="{ 'is-invalid': erros.email }"
              placeholder="ana@exemplo.com"
              @blur="validarEmail"
            />
            <div v-if="erros.email" class="text-danger mt-2 small">{{ erros.email }}</div>
          </div>

          <div class="col-md-6">
            <label for="dataNascimento" class="form-label fw-semibold">Data de Nascimento</label>
            <input
              id="dataNascimento"
              v-model="novoTutor.dataNascimento"
              type="date"
              class="form-control"
              :class="{ 'is-invalid': erros.dataNascimento }"
              @blur="validarDataNascimento"
            />
            <div v-if="erros.dataNascimento" class="text-danger mt-2 small">{{ erros.dataNascimento }}</div>
          </div>

          <div class="col-12 mt-4">
            <h2 class="section-title mb-3">Endereço</h2>
          </div>

          <div class="col-md-8">
            <label for="rua" class="form-label fw-semibold">Rua</label>
            <input
              id="rua"
              v-model="novoTutor.rua"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.rua }"
              placeholder="Digite o nome da rua"
              @blur="validarCampoObrigatorio('rua', 'Ops, campo obrigatório, por favor preencha-o!')"
            />
            <div v-if="erros.rua" class="text-danger mt-2 small">{{ erros.rua }}</div>
          </div>

          <div class="col-md-4">
            <label for="numero" class="form-label fw-semibold">Número</label>
            <input
              id="numero"
              v-model="novoTutor.numero"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.numero }"
              placeholder="Número"
              @blur="validarCampoObrigatorio('numero', 'Ops, campo obrigatório, por favor preencha-o!')"
            />
            <div v-if="erros.numero" class="text-danger mt-2 small">{{ erros.numero }}</div>
          </div>

          <div class="col-md-6">
            <label for="bairro" class="form-label fw-semibold">Bairro</label>
            <input
              id="bairro"
              v-model="novoTutor.bairro"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.bairro }"
              placeholder="Digite o bairro"
              @blur="validarCampoObrigatorio('bairro', 'Ops, campo obrigatório, por favor preencha-o!')"
            />
            <div v-if="erros.bairro" class="text-danger mt-2 small">{{ erros.bairro }}</div>
          </div>

          <div class="col-md-6">
            <label for="cidade" class="form-label fw-semibold">Cidade</label>
            <input
              id="cidade"
              v-model="novoTutor.cidade"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.cidade }"
              placeholder="Digite a cidade"
              @blur="validarCampoObrigatorio('cidade', 'Ops, campo obrigatório, por favor preencha-o!')"
            />
            <div v-if="erros.cidade" class="text-danger mt-2 small">{{ erros.cidade }}</div>
          </div>

          <div class="col-md-6">
            <label for="estado" class="form-label fw-semibold">Estado</label>
            <select
              id="estado"
              v-model="novoTutor.estado"
              class="form-select"
              :class="{ 'is-invalid': erros.estado }"
              @blur="validarCampoObrigatorio('estado', 'Ops, campo obrigatório, por favor preencha-o!')"
            >
              <option value="">Selecione</option>
              <option v-for="estado in estados" :key="estado" :value="estado">{{ estado }}</option>
            </select>
            <div v-if="erros.estado" class="text-danger mt-2 small">{{ erros.estado }}</div>
          </div>

          <div class="col-md-6">
            <label for="cep" class="form-label fw-semibold">CEP</label>
            <input
              id="cep"
              :value="novoTutor.cep"
              @input="novoTutor.cep = formatarCep($event.target.value)"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': erros.cep }"
              placeholder="00000-000"
              @blur="validarCampoObrigatorio('cep', 'Ops, campo obrigatório, por favor preencha-o!')"
            />
            <div v-if="erros.cep" class="text-danger mt-2 small">{{ erros.cep }}</div>
          </div>

          <div class="col-12 mt-4">
            <div class="alert alert-info d-flex align-items-center gap-2 mb-0">
              <span class="info-icon">i</span>
              <span>Preencha corretamente Nome e Telefone para habilitar o cadastro.</span>
            </div>
          </div>

          <div class="col-12 d-flex justify-content-end gap-2 mt-4">
            <RouterLink class="btn btn-outline-secondary" :to="{ name: 'tutores' }">
              Cancelar
            </RouterLink>
            <button type="submit" class="btn btn-primary" :disabled="!formularioValido">
              Cadastrar Tutor
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page-shell {
  width: 100%;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.section-title {
  margin: 0;
  font-size: 2rem;
  font-weight: 700;
  color: #1f2937;
}

.info-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 1.5rem;
  height: 1.5rem;
  border-radius: 999px;
  background: #0d6efd;
  color: #fff;
  font-size: 0.8rem;
  font-weight: 700;
}

.form-control,
.form-select {
  border-radius: 0.5rem;
  min-height: 46px;
}

.btn {
  min-width: 140px;
}
</style>
