<script setup lang="ts">
import Card from './components/Card.vue'
import { ref, onMounted } from 'vue'
import axios from 'axios'

interface Filme {
  id: number
  titulo: string
  diretor: string
  ano: number
  nota: number
}

const filmes = ref<Filme[]>([])

const titulo = ref('')
const diretor = ref('')
const ano = ref<number | ''>('')
const nota = ref<number | ''>('')
const idEdicao = ref<number | null>(null)

function limparCampos() {
  titulo.value = ''
  diretor.value = ''
  ano.value = ''
  nota.value = ''
}

function editarFilme(filme: Filme) {
  idEdicao.value = filme.id

  titulo.value = filme.titulo
  diretor.value = filme.diretor
  ano.value = filme.ano
  nota.value = filme.nota
}

async function obterFilmes() {
  const resposta = await axios.get('https://api-lpv.onrender.com/filmes')
  filmes.value = resposta.data.data
}

async function salvarFilme() {

  if (
    titulo.value.trim() === '' ||
    diretor.value.trim() === '' ||
    ano.value === '' ||
    nota.value === ''
  ) {
    alert('Preencha todos os campos!')
    return
  }

  if (nota.value > 10 || nota.value < 0) {
    alert('A nota deve ser entre 0 e 10!')
    return
  }

  const anoAtual = new Date().getFullYear()

if (ano.value > anoAtual) {
  alert(`O ano do filme não pode ser maior que ${anoAtual}!`)
  return
}

  const novoFilme = {
    titulo: titulo.value,
    diretor: diretor.value,
    ano: ano.value,
    nota: nota.value
  }

  if (idEdicao.value !== null) {
    await axios.patch(
      `https://api-lpv.onrender.com/filmes/${idEdicao.value}`,
      novoFilme
    )

    idEdicao.value = null
  } else {
    await axios.post(
      'https://api-lpv.onrender.com/filmes',
      novoFilme
    )
  }

  limparCampos()

  await obterFilmes()
}

async function excluirFilme(id: number) {
  await axios.delete(`https://api-lpv.onrender.com/filmes/${id}`)
  await obterFilmes()
}

onMounted(() => {
  obterFilmes()
})
</script>

<template>
  <main class="pagina">
    <header class="cabecalho">
      <h1>Meus filmes</h1>
      <p>Um espaço para reunir seus filmes e suas notas.</p>
    </header>

    <section class="cadastro" aria-labelledby="titulo-cadastro">
      <h2 id="titulo-cadastro">Cadastro de filmes</h2>
      <p class="orientacao">Todos os campos serão obrigatórios no cadastro.</p>

      <div class="campos">
        <div class="campo">
          <label for="titulo">Título</label>
          <input id="titulo" name="titulo" type="text" placeholder="Nome do filme" v-model="titulo" />
        </div>
        <div class="campo">
          <label for="diretor">Diretor</label>
          <input id="diretor" name="diretor" type="text" placeholder="Nome do diretor" v-model="diretor" />
        </div>
        <div class="campo">
          <label for="ano">Ano</label>
          <input id="ano" name="ano" type="number" placeholder="Ex.: 2014" v-model.number="ano" />
        </div>
        <div class="campo">
          <label for="nota">Nota</label>
          <input id="nota" name="nota" type="number" placeholder="Ex.: 10" v-model.number="nota" />
        </div>
        <button class="salvar" type="button" @click="salvarFilme">
          {{ idEdicao !== null ? 'Atualizar' : 'Salvar' }}
        </button>
      </div>
    </section>

    <section class="filmes" aria-labelledby="titulo-filmes">
      <h2 id="titulo-filmes">Filmes cadastrados</h2>
      <div class="lista-filmes">
        <Card v-for="filme in filmes" :key="filme.id" :filme="filme" @excluir="excluirFilme" @editar="editarFilme" />
      </div>
    </section>
  </main>
</template>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #f4f6fa;
  color: #233047;
  font-family: Arial, sans-serif;
  line-height: 1.5;
}

button,
input {
  font: inherit;
}

button {
  cursor: pointer;
}

button:focus-visible,
input:focus-visible {
  outline: 3px solid #5278cc;
  outline-offset: 3px;
}
</style>

<style scoped>
.pagina {
  max-width: 1040px;
  margin: 0 auto;
  padding: 48px 24px;
}

.cabecalho {
  margin-bottom: 28px;
}

h1,
h2,
p {
  margin: 0;
}

h1 {
  font-size: 32px;
}

h2 {
  font-size: 21px;
}

.cabecalho p,
.orientacao {
  margin-top: 6px;
  color: #5c687a;
}

.cadastro {
  padding: 28px;
  border: 1px solid #dce2eb;
  border-radius: 12px;
  background: #fff;
}

.orientacao {
  font-size: 14px;
}

.campos {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(0, 1fr) auto;
  gap: 20px;
  align-items: end;
  margin-top: 24px;
}

.campo {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.campo:nth-child(3) {
  grid-column: 1;
}

label {
  font-size: 13px;
  font-weight: 700;
  text-transform: uppercase;
}

input {
  width: 100%;
  min-width: 0;
  height: 46px;
  padding: 10px 12px;
  border: 1px solid #bac4d3;
  border-radius: 6px;
  color: #233047;
  background: #fff;
}

.salvar {
  height: 46px;
  padding: 10px 28px;
  border: 1px solid #3159a6;
  border-radius: 6px;
  background: #3159a6;
  color: #fff;
  font-weight: 700;
}

.salvar:hover {
  background: #264782;
}

.filmes {
  margin-top: 36px;
}

.lista-filmes {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 20px;
  margin-top: 18px;
}

@media (max-width: 640px) {
  .pagina {
    padding: 28px 16px;
  }

  .cadastro {
    padding: 20px;
  }

  .campos,
  .lista-filmes {
    grid-template-columns: 1fr;
  }
}
</style>
