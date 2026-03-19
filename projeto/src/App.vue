<template>
  <div class="app">
    <header class="topbar">
      <div class="title">
        <span class="gt">GT</span>
        <h1>Gerenciador de Tarefas</h1>
      </div>
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
    <div class="apresentacao">
      <nav class="selecao">          
        <p>VISUALIZAÇÕES</p>
        <button
        v-for="v in views"
        :key="v.id"
        class="view"
        :class="{ 'view-ativa': viewAtual === v.id }" 
        @click="viewAtual = v.id">
          <OptionsView
          :optionView="v"
          />
        </button>
      </nav>
      <div class="infos">
        <div v-if="viewAtual === 2">
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
        </div>

        <div v-else-if="viewAtual === 1">
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

        <div v-else-if="viewAtual===3">
          <div class="board">
            <div class="coluna">
                <h2>Todas as tarefas</h2>
                <div
                  v-for="t in todasTarefas"
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
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import CardTarefa from "./CardTarefa.vue"
import type { Tarefa } from "./CardTarefa.vue"
import { ref, computed } from "vue"
import type { Desenvolvedor } from "./Desenvolvedores.vue"
import OptionsView from "./OptionsView.vue"
import type { optionView } from "./OptionsView.vue"
import dev from "./icons/dev.vue"
import pasta from "./icons/pasta.vue"
import quadro from "./icons/quadro.vue"

const tarefas = ref<Tarefa[]>([])
const novaTarefa = ref("")
const optionsView = OptionsView
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
const viewAtual = ref(1)
const views = ref<optionView[]>([
  {
    id: 1,
    nome: "Quadro Kanban",
    image: quadro
  },
  {
    id: 2,
    nome: "Por Desenvolvedor",
    image: dev
  },
  {
    id: 3,
    nome: "Todas as Tarefas",
    image: pasta
  }
])

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

* {
  box-sizing: border-box;
}

.app {
  background: #000000;
  min-height: 100vh;  
  color: #EEEEEE;
  font-family: sans-serif;
  font-weight: normal;
}

.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px 0 20px; 
  border-bottom: 1px solid #0E0E0E;
}

.topbar .title{
  display: flex;
  align-items: center;
}

.topbar .gt{
  border-radius: 5px;
  font-weight: bold;
  padding: 6px;
  margin-right: 5px;
  color: black;
  background: #EEEEEE;
}

.topbar h1 {
  font-size: 24px;
  font-weight: bold;
}

.selecao {
  font-size: 12px;
  padding: 20px 20px 20px 10px; /* Dei um pouco mais de respiro (padding) na direita para a borda não grudar no botão */
  font-family: sans-serif;
  font-weight: bold;
  color: #808080;
  min-width: 220px; /* Largura mínima previsível para o seu menu */
  min-height: 100vh; /* Faz a borda lateral ir até o fim da tela */
  border-right: 1px solid #0E0E0E;  
  display: flex;
  flex-direction: column;
  align-items: stretch;
  box-sizing: border-box; /* Garante que o padding não quebre o tamanho final */
  overflow-x: auto;
}

.apresentacao {
  display: flex;
  box-sizing: border-box;
  /* Removido o justify-content: space-around; pois usaremos flex: 1 nos filhos */
}

.infos {
  flex: 1; /* A mágica acontece aqui: isso manda a div ocupar todo o espaço restante da tela, sem invadir o menu */
  padding: 20px;
  min-width: 0; /* Previne um bug comum do flexbox onde o conteúdo interno força a div a vazar da tela */
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
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
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

.view {
  background-color: #000000; 
  margin-bottom: 5px;
  display: flex;
  align-items: center;
  justify-content: flex-start; /* Garante que o ícone e o texto fiquem alinhados à esquerda */
  padding: 8px 12px;
  background: none;
  border: none;
  color: #808080;
  box-sizing: border-box;
  text-transform: none;
  gap: 5px;
  white-space: nowrap;
  border-radius: 15px;
  width: 100%; /* Faz o fundo do botão preencher o menu corretamente */
  flex-shrink: 0;
}

.view:hover{
  background-color: #0A0A0A;
  color: #EEEEEE;
}

.view:active{
  transform: scale(0.96);
}

.view-ativa {
  background-color: #121212;
  color: #EEE8E0;
}

.view-ativa .icone-svg {
  color: #EEE8E0;
}

@media (max-width: 768px) {
  .apresentacao {
    flex-direction: column;
  }

  .selecao {
    flex-direction: row;
    min-width: 100%;
    min-height: auto;
    border-right: none;
    border-bottom: 1px solid #0E0E0E;
    overflow-x: auto;
  }

  .view{
    width: auto;
  }

  .infos {
    padding: 10px;
  }

  .board {
    grid-template-columns: 1fr;
  }

  .topbar {
    flex-direction: column;
    align-items: flex-start;
    gap: 10px;
  }

  .nova {
    width: 100%;
    flex-wrap: wrap;
  }

  .nova input,
  .nova select,
  .nova button {
    flex: 1;
    min-width: 120px;
  }
}
</style>