<template>
    <nav class="selecao">
        <p>VISUALIZAÇÕES</p>
        <button>
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-layout-grid h-4 w-4" aria-hidden="true"><rect width="7" height="7" x="3" y="3" rx="1"></rect><rect width="7" height="7" x="14" y="3" rx="1"></rect><rect width="7" height="7" x="14" y="14" rx="1"></rect><rect width="7" height="7" x="3" y="14" rx="1"></rect></svg>
            Quadro Kanban
        </button>
    </nav>
</template>

<script setup lang="ts">
    export interface OptionView{
        
    }
</script>

<style scoped>

.selecao{
  font-size: 12px;
  padding: 10px;
  font-family: sans-serif;
  font-weight: bold;
  color: #808080;
  width: 100%;
  height: 100%;
  border-right: 1px solid #0E0E0E;
  box-sizing: border-box;
}
button{
    display: flex;
    align-items: center;
    padding: 8px 12px;
    background: none;
    border: none;
    color: #808080;
    box-sizing: border-box;
    text-transform: none;
    width: 199px;
    gap: 5px;
}
</style>