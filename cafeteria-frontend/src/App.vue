<script setup>
import { ref, onMounted } from 'vue'
import api from './services/api'

// Uma lista reativa de produtos, começando vazia
const produtos = ref([])
const carregador = ref(true)

async function buscarProdutos() {
  carregando.value = true
}

function incrementar() {
  contador.value++
}
</script>

<template>
  <h1>Cafeteria! ☕</h1>
  <p>Cliques: {{contador}}</p>
  <button @click="incrementar">Clicar</button>
</template>

<style scoped>
h1 {
  text-align: center;
  color: #8b4513;
}
</style>
