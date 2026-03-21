<template>
  <div class="box_card">
    <div class="top">
      <span class="titulo">{{ tarefa.titulo }}</span>
      <span v-if="props.devNome" class="dev">
        👨‍💻 {{ props.devNome }}
      </span>
      <span
        class="status"
        :class="tarefa.status"
      >
        {{ statusFormatado() }}
      </span>
    </div>

    <div class="acoes">

      <button
        v-if="props.editavel && ['paraFazer', 'pausado'].includes(tarefa.status)"
        class="progresso"
        @click="mudarStatus('progresso')"
      >
        Em Progresso
      </button>

      <button
        v-if="props.editavel && tarefa.status === 'progresso'"
        class="concluir"
        @click="mudarStatus('concluido')"
      >
        Concluir
      </button>

      <button
        v-if="props.editavel && tarefa.status === 'progresso'"
        class="pausado"
        @click="mudarStatus('pausado')"
      >
        Pausar
      </button>

      <button
        v-if="props.editavel"
        class="remover"
        @click="remover"
      >
        Remover
      </button>
    </div>
  </div>
</template>


<script setup lang="ts">
export interface Tarefa {
  id: number
  titulo: string
  status: "paraFazer" | "progresso" | "concluido" | "pausado"
}

const props = defineProps<{
  tarefa: Tarefa
  editavel?: boolean
  devNome?: string
}>()

const emit = defineEmits<{
  (e: "remover", id: number): void
  (e: "alterarStatus", payload: { id: number; status: Tarefa["status"] }): void
}>()

function remover() {
  emit("remover", props.tarefa.id)
}

function mudarStatus(status: Tarefa["status"]) {
  emit("alterarStatus", {
    id: props.tarefa.id,
    status
  })
}
function statusFormatado() {
  switch (props.tarefa.status) {
    case "paraFazer":
      return "Para Fazer"
    case "progresso":
      return "Em Progresso"
    case "concluido":
      return "Concluído"
    case "pausado":
      return "Pausado"
  }
}
</script>


<style scoped>
.box_card {
  background: #1e293b;
  padding: 14px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  transition: 0.2s;
}

.box_card:hover {
  background: #263449;
  transform: translateY(-2px);
}

.top {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.titulo {
  font-weight: 500;
  font-size: 14px;
  color: #f1f5f9;
}

.status {
  font-size: 11px;
  padding: 4px 8px;
  border-radius: 20px;
  text-transform: capitalize;
}

.status.pausado {
  background: #8e9100;
  color: #ffffff;
}

.status.paraFazer {
  background: #ffffff;
  color: #000000;
}

.status.progresso {
  background: #1d4ed8;
  color: #ffffff;
}

.status.concluido {
  background: #15803d;
  color: #ffffff;
}

.acoes {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

button {
  border: none;
  border-radius: 8px;
  padding: 6px 10px;
  font-size: 12px;
  cursor: pointer;
  transition: 0.2s;
}

.progresso {
  background: #2564eba8;
  color: white;
}

.progresso:hover {
  background: #1d4ed8;
}

.concluir {
  background: #16a34aa8;
  color: white;
}

.concluir:hover {
  background: #15803d;
}

.pausado {
  background: #c4c700a8;
  color: #ffffff;
}

.pausado:hover {
  background: #8e9100;
  color: #ffffff;
}

.remover {
  background: #dc2626a8;
  color: white;
}

.remover:hover {
  background: #b91c1c;
}

.dev {
  font-size: 12px;
  color: #94a3b8;
  margin-left: 6px;
}
</style>