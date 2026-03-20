<template>
  <div class="login-container">
    <div class="login-box">
      <h1>Desafio Bext Front-End</h1>

      <input
        v-model="usuario"
        placeholder="Usuário"
        required
      />

      <input
        v-model="senha"
        type="password"
        placeholder="Senha"
        @keyup.enter="fazerLogin"
        required
      />

      <button @click="fazerLogin">Entrar</button>

      <p v-if="erro" class="erro">{{ erro }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue"

const usuario = ref("")
const senha = ref("")
const erro = ref("")

const emit = defineEmits(["loginSucesso"])

function fazerLogin() {
  erro.value = ""

  const usuarioValido = usuario.value.toLowerCase() === "bext"
  const senhaValida = senha.value === "desafio"

  if (usuarioValido && senhaValida) {
    emit("loginSucesso")
  } else {
    erro.value = "Usuário ou senha inválidos"
  }
}
</script>

<style scoped>
.login-container {
  height: 100vh;
  background: #000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.login-box {
  background: #1e293b;
  padding: 30px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  gap: 15px;
  width: 300px;
}

.login-box h1 {
  text-align: center;
  font-size: 20px;
  color: #ffffff;
}

.login-box input {
  padding: 10px;
  border-radius: 6px;
  border: none;
  background: #334155;
  color: white;
}

.login-box button {
  background: #3b82f6;
  border: none;
  padding: 10px;
  border-radius: 6px;
  color: white;
  cursor: pointer;
}

.login-box button:hover {
  background: #2563eb;
}

.erro {
  color: #ef4444;
  font-size: 14px;
  text-align: center;
}
</style>