<template>
  <div class="app">
    <header class="topbar">
      <h1>Gerenciador de Tarefas</h1>

      <div class="nova">
        <input
          v-model="novaTarefa"
          placeholder="Nova tarefa..."
          @keyup.enter="adicionarTarefa"
        />
        <button @click="adicionarTarefa">Adicionar</button>
      </div>
    </header>

    <div class="board">
      <div class="coluna">
        <h2>Para Fazer ({{ tarefasFazer.length }})</h2>
        <div
          v-for="t in tarefasFazer"
          :key="t.id"
          class="card"
        >
          <CardTarefa
            :tarefa="t"
            @remover="removerTarefa"
            @alterarStatus="alterarStatus"
          />
        </div>
      </div>

      <div class="coluna">
        <h2>Em Progresso ({{ tarefasProgresso.length }})</h2>
        <div
          v-for="t in tarefasProgresso"
          :key="t.id"
          class="card"
        >
          <CardTarefa
            :tarefa="t"
            @remover="removerTarefa"
            @alterarStatus="alterarStatus"
          />
        </div>
      </div>

      <div class="coluna">
        <h2>Concluído ({{ tarefasConcluidas.length }})</h2>
        <div
          v-for="t in tarefasConcluidas"
          :key="t.id"
          class="card"
        >
          <CardTarefa
            :tarefa="t"
            @remover="removerTarefa"
            @alterarStatus="alterarStatus"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import CardTarefa from "./CardTarefa.vue"
import type { Tarefa } from "./CardTarefa.vue"
import { ref, computed } from "vue"

const tarefas = ref<Tarefa[]>([])
const novaTarefa = ref("")
let contadorId = 1

function adicionarTarefa() {
  if (!novaTarefa.value.trim()) return

  tarefas.value.push({
    id: contadorId++,
    titulo: novaTarefa.value,
    status: "paraFazer",
  })

  novaTarefa.value = ""
}

function removerTarefa(id: number) {
  tarefas.value = tarefas.value.filter(t => t.id !== id)
}

function alterarStatus(payload: { id: number; status: Tarefa["status"] }) {
  const tarefa = tarefas.value.find(t => t.id === payload.id)
  if (tarefa) {
    tarefa.status = payload.status
  }
}

const tarefasFazer = computed(() =>
  tarefas.value.filter(t => t.status === "paraFazer")
)

const tarefasProgresso = computed(() =>
  tarefas.value.filter(t => t.status === "progresso" || t.status === "pausado" )
)

const tarefasConcluidas = computed(() =>
  tarefas.value.filter(t => t.status === "concluido")
)
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.app {
  background: #0f172a;
  min-height: 100vh;
  color: #f1f5f9;
  font-family: Inter, sans-serif;
  padding: 30px;
}

.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 40px;
}

.topbar h1 {
  font-size: 24px;
  font-weight: bold;
}

.nova {
  display: flex;
  gap: 10px;
}

.nova input {
  padding: 8px;
  border-radius: 6px;
  border: none;
  background: #1e293b;
  color: white;
}

.nova button {
  background: #3b82f6;
  border: none;
  padding: 8px 14px;
  border-radius: 6px;
  color: white;
  cursor: pointer;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

.coluna {
  background: #1e293b;
  padding: 15px;
  border-radius: 12px;
  min-height: 500px;
}

.coluna h2 {
  margin-bottom: 15px;
  font-size: 16px;
}

.card {
  background: #334155;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 10px;
  transition: 0.2s;
}

.card:hover {
  transform: translateY(-2px);
  background: #3f4d66;
}
</style>