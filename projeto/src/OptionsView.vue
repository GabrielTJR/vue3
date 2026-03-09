<template>
    <div class="option">
        <component :is="optionView.image" class="icone-svg" />
        {{optionView.nome}}
    </div>
</template>

<script setup lang="ts">
    export interface optionView{
        id: number
        nome: string
        image: any
    }
    const props = defineProps<{
        optionView: optionView
    }>()
</script>

<style scoped>
    .option{
    display:flex;
    align-items:center;
    gap:6px;
    }
</style>