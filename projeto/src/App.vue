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

        <select v-model="devSelecionado">
          <option disabled :value="null">Selecionar dev</option>
          <option
            v-for="dev in desenvolvedores"
            :key="dev.id"
            :value="dev.id"
          >
            {{ dev.nome }}
          </option>
        </select>

        <button @click="adicionarTarefa">Adicionar</button>
      </div>
    </header>

    <h2>Por Desenvolvedor</h2>
    <div class="board">
      <div
        class="coluna"
        v-for="dev in desenvolvedores"
        :key="dev.id"
      >
        <h2>{{ dev.nome }} ({{ dev.cargo }})</h2>

        <div
          v-for="t in dev.tarefas"
          :key="t.id"
          class="card"
        >
          <CardTarefa
            :tarefa="t"
            :editavel="true"
            @remover="removerTarefa"
            @alterarStatus="alterarStatus"
          />
        </div>

      </div>
    </div>

    <h2>Geral</h2>
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
            :devNome="encontrarDevDaTarefa(t.id)"
            :editavel="false"
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
            :devNome="encontrarDevDaTarefa(t.id)"
            :editavel="false"
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
            :devNome="encontrarDevDaTarefa(t.id)"
            :editavel="false"
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
import type { Desenvolvedor } from "./Desenvolvedores.vue"

const tarefas = ref<Tarefa[]>([])
const novaTarefa = ref("")
const devSelecionado = ref<number | null>(null)
const desenvolvedores = ref<Desenvolvedor[]>([
  {
    id: 1,
    nome: "Gabriel",
    cargo: "Senior",
    tarefas: []
  },
  {
    id: 2,
    nome: "Camila",
    cargo: "Pleno",
    tarefas: []
  },
  {
    id: 3,
    nome: "Diego",
    cargo: "Junior",
    tarefas: []
  }
])
let contadorId = 1

function encontrarDevDaTarefa(tarefaId: number) {
  const dev = desenvolvedores.value.find(dev =>
    dev.tarefas.some(t => t.id === tarefaId)
  )

  return dev?.nome  
}

function adicionarTarefa() {
  if (!novaTarefa.value.trim()) return
  if (!devSelecionado.value) return

  const dev = desenvolvedores.value.find(
    d => d.id === devSelecionado.value
  )

  if (!dev) return

  dev.tarefas.push({
    id: contadorId++,
    titulo: novaTarefa.value,
    status: "paraFazer"
  })

  novaTarefa.value = ""
}

function removerTarefa(id: number) {
  desenvolvedores.value.forEach(dev => {
    dev.tarefas = dev.tarefas.filter(t => t.id !== id)
  })
}

function alterarStatus(payload: { id: number; status: Tarefa["status"] }) {

  desenvolvedores.value.forEach(dev => {

    const tarefa = dev.tarefas.find(t => t.id === payload.id)

    if (tarefa) {
      tarefa.status = payload.status
    }

  })
}

const todasTarefas = computed(() =>
  desenvolvedores.value.flatMap(dev => dev.tarefas)
)

const tarefasFazer = computed(() =>
  todasTarefas.value.filter(t => t.status === "paraFazer")
)

const tarefasProgresso = computed(() =>
  todasTarefas.value.filter(t => t.status === "progresso" || t.status === "pausado")
)

const tarefasConcluidas = computed(() =>
  todasTarefas.value.filter(t => t.status === "concluido")
)
</script>

<style scoped>
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

.nova select {
  padding: 8px;
  border-radius: 6px;
  border: none;
  background: #1e293b;
  color: white; 
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