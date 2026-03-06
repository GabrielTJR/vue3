<script setup lang="ts">
    import type {Tarefa} from './CardTarefa.vue'
    export interface Desenvolvedor{
        id: number
        nome: string
        cargo: "Senior" | "Pleno" | "Junior"
        tarefas: Tarefa[]
    }
</script>