<template>
  <div class="card">
    <div class="top">
      <span class="titulo">{{ tarefa.titulo }}</span>

      <span
        class="badge"
        :class="tarefa.status"
      >
        {{ statusFormatado() }}
      </span>
    </div>

    <div class="acoes">
      <button
        v-if="tarefa.status === 'paraFazer'"
        class="progresso"
        @click="mudarStatus('progresso')"
      >
        Em Progresso
      </button>

      <button
        v-if="tarefa.status === 'progresso'"
        class="concluir"
        @click="mudarStatus('concluido')"
      >
        Concluir
      </button>

      <button
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
  status: "paraFazer" | "progresso" | "concluido"
}

const props = defineProps<{
  tarefa: Tarefa
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
    status,
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
  }
}
</script>


<style scoped>
.card {
  background: #1e293b;
  padding: 14px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  transition: 0.2s;
}

.card:hover {
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

.badge {
  font-size: 11px;
  padding: 4px 8px;
  border-radius: 20px;
  text-transform: capitalize;
}

.badge.paraFazer {
  background: #b8b500;
  color: white;
}

.badge.progresso {
  background: #1d4ed8;
  color: white;
}

.badge.concluido {
  background: #15803d;
  color: white;
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
  background: #2563eb;
  color: white;
}

.progresso:hover {
  background: #1d4ed8;
}

.concluir {
  background: #16a34a;
  color: white;
}

.concluir:hover {
  background: #15803d;
}

.remover {
  background: #dc2626;
  color: white;
}

.remover:hover {
  background: #b91c1c;
}
</style>