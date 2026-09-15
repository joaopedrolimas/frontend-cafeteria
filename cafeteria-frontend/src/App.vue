<script setup>
import { ref, onMounted } from 'vue'
import api from './services/api'

// Uma lista reativa de produtos, começando vazia
const produtos = ref([])
const carregador = ref(true)
const produtoEditandoId = ref(null)

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

function editarProduto(produto) {
  produtoEditandoId.value = produto.id
  // Copia os dados do produto para o formulário
  novoProduto.value = { ...produto }

  async function salvarProduto() {
    if (produtoEditandoId.value) {
      // Modo edição: PUT
      await api.put(`/produtos/${produtoEditandoId.value}`, novoProduto.value)
    } else {
      // Modo criação: POST
      await api.post('/produtos', novoProduto.value)
    }

    cancelarEdicao()
    await buscarProdutos()
  }

  function cancelarEdicao() {
    produtoEditandoId.value = null
    novoProduto.value = { nome: '', categoria: '', preco: 0, disponivel: true }

async function criarProduto() {
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

  <form @submit.prevent="criarProduto">
    <input v-model="novoProduto.nome" placeholder="Nome do produto" required />
    <input v-model="novoProduto.categoria" placeholder="Categoria" required />
    <input v-model.number="novoProduto.preco" type="number" step="0.01" placeholder="Preço" required />
    <label>
      <input v-model="novoProduto.disponivel" type="checkbox" />
    </label>
    <button type="submit">Cadastrar Produto</button>
  </form>

  <ul>
    <li v-for="produto in produtos" :key="produto.id">
      {{ produto.nome }} - R$ {{ produto.preco.toFixed(2) }}
    </li>
  </ul>
</template>

<style scoped>
h1 {
  text-align: center;
  color: #8b4513;
}
</style>
