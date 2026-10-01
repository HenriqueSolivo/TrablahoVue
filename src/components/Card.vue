<script setup lang="ts">
defineProps<{
  filme: {
    id: number
    titulo: string
    diretor: string
    ano: number
    nota: number
  }
}>()

const emit = defineEmits(['excluir', 'editar'])

</script>

<template>
  <article class="card">
    <h3>{{ filme.titulo }}</h3>
    <dl class="detalhes">
      <div>
        <dt>Diretor</dt>
        <dd>{{ filme.diretor }}</dd>
      </div>
      <div>
        <dt>Ano</dt>
        <dd>{{ filme.ano }}</dd>
      </div>
      <div>
        <dt>Nota</dt>
        <dd>{{ filme.nota }}</dd>
      </div>
    </dl>
    <div class="acoes">
      <button class="editar" type="button" @click="emit('editar', filme)">
        Editar
      </button>
      <button class="excluir" type="button" @click="emit('excluir', filme.id)">
        Excluir
      </button>
    </div>
  </article>
</template>

<style scoped>
.card {
  padding: 24px;
  border: 1px solid #dce2eb;
  border-radius: 12px;
  background: #fff;
  overflow-wrap: anywhere;
}

h3 {
  margin: 0 0 20px;
  font-size: 22px;
}

.detalhes {
  display: grid;
  gap: 12px;
  margin: 0;
}

.detalhes div {
  display: grid;
  grid-template-columns: 70px minmax(0, 1fr);
  gap: 12px;
}

dt {
  color: #5c687a;
}

dd {
  margin: 0;
  font-weight: 600;
}

.acoes {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 24px;
  padding-top: 20px;
  border-top: 1px solid #e7ebf1;
}

button {
  padding: 8px 20px;
  border: 1px solid;
  border-radius: 6px;
  font-weight: 600;
}

.editar {
  border-color: #bbcee9;
  background: #eef4ff;
  color: #284f96;
}

.editar:hover {
  background: #dfeaff;
}

.excluir {
  border-color: #ebc2c2;
  background: #fff4f4;
  color: #a12c2c;
}

.excluir:hover {
  background: #ffe5e5;
}
</style>
