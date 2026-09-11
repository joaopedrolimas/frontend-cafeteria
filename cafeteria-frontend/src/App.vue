<script setup>
import { ref, onMounted } from 'vue'
import api from './services/api'

// Uma lista reativa de produtos, começando vazia
const produtos = ref([])
const carregador = ref(true)

const novoProduto = ref({
  nome: '',
  categoria: '',
  preco: 0,
  disponivel: true
})

async function buscarProdutos() {
  const resposta = await api.get('/produtos')
  produtos.value = resposta.data
}

async function ciarProduto() {
  try {
    await api.post('/produtos', novoProduto.value)

    // Limpa o formulário depois de guardar
    novoProduto.value = {nome: '', categoria: '', preco: 0, disponivel: true }

    // Atualiza a lista com o novo produto
    await buscarProdutos()
  } catch (erro) {
    console.error('Erro ao criar produto:', erro)
  }
}

onMounted(buscarProdutos)
</script>

<template>
  <h1>Cardapio da Cafeteria ☕</h1>

  <p v-if="carregando">A carregar produtos...</p>

  <ul v-else>
      <li v-for="produto in produtos" :key="produto.id">
        {{ produto.nome }} - R$ {{ produto.preco.toFixed(2) }}
        <span v-if="!produto.disponivel"> (indisponivel)</span>
      </li>
  </ul>

  <p>Cliques: {{contador}}</p>
  <button @click="incrementar">Clicar</button>
</template>

<style scoped>
h1 {
  text-align: center;
  color: #8b4513;
}
</style>
