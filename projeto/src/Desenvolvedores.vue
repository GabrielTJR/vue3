<script setup lang="ts">
    import type {Tarefa} from './CardTarefa.vue'
    export type Cargos = "Senior" | "Pleno" | "Junior"
    export interface Desenvolvedor{
        id: number
        nome: string
        cargo: Cargos
        tarefas: Tarefa[]
    }
</script>