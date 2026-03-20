<script setup>
 import { ref, computed } from 'vue'
 let filtro = ref('');
 let posicaoEditar = ref(-1);
 let tarefas = ref([
   { id: 1, desc: 'Estudar VueJs', status: 'pendente'},
   { id: 2, desc: 'Fazer todo-list', status: 'pendente'},
   { id: 3, desc: 'Deploy contador Vue', status: 'concluida'}
 ])


 let pendentes = computed(() => {
 return tarefas.value.filter(item => item.status.includes('pendente')).length;
 })
 let concluidas = computed(() => {
 return tarefas.value.filter(item => item.status.includes('concluida')).length;
 })


  let tarefasFiltradas = computed(() => {
   if(filtro.value.trim().length > 0){
     return tarefas.value.filter(item => item.desc.includes(filtro.value));
   }
   else{
     return tarefas.value;
   }
 })
  const novaTarefa = ref('');


 function adicionar() {
   if (novaTarefa.value.length >= 5){
     if (posicaoEditar.value === -1){
     let maiorNumero;
      if (tarefas.value.length === 0) {
        maiorNumero = 0;
      } else {
        maiorNumero = Math.max(...tarefas.value.map(item => item.id));
      }
      tarefas.value.push({
     id: maiorNumero + 1,
     desc: novaTarefa.value,
     status: 'pendente'
    });
   novaTarefa.value = '';
    
   } else {
     tarefas.value[posicaoEditar.value].desc = novaTarefa.value
     posicaoEditar.value = -1;
     novaTarefa.value = '';
   }
   }
 }


 function marcarConcluida(id) {
   const posicao = tarefas.value.findIndex(item => item.id === id);
   if (tarefas.value[posicao].status === 'concluida'){
     tarefas.value[posicao].status = 'pendente';
   } else {
     tarefas.value[posicao].status = 'concluida'
   }
 }


 function deletar(id){
   const posicao = tarefas.value.findIndex(item => item.id === id);
   tarefas.value.splice(posicao, 1);
 }


 function editar(id){
   const posicao = tarefas.value.findIndex(item => item.id === id);
   posicaoEditar.value = posicao;
   novaTarefa.value = tarefas.value[posicao].desc
 }


</script>


<template>
 <div class="container">
   <h1>Lista de Tarefas</h1>
   <input type="text" v-model="novaTarefa" @keyup.enter="adicionar" @input="novaTarefa.length < 5 ? aviso = true : aviso = false">
   <button @click="adicionar">Adicionar</button>
   <ul>
     <li v-for="item in tarefasFiltradas" :key="item.id">
       <span @click="marcarConcluida(item.id)" :class="{ concluida: item.status === 'concluida'}">{{ item.desc }}</span>
       <button @click="deletar(item.id)">Deletar</button>
       <button @click="editar(item.id)">Editar</button>
     </li>
   </ul>
   <input type="text" placeholder="Filtrar tarefa" v-model="filtro">
    <button v-on:click="tarefas.sort((a, b) => a.desc.localeCompare(b.desc, 'pt-BR'))">Ordenar</button>
    <div class="contagem">
     <p>Pendentes: {{ pendentes }}</p>
     <p>Concluidas: {{ concluidas }}</p>
   </div>
 </div>
</template>


<style scoped>


.concluida{
 text-decoration: line-through;
}


li{
 cursor: pointer;
}


button{
 margin: 0 0 0 5px;
 color: black;
 background-color: cadetblue;
 border-radius: 8px;
}


h1{
 color: cadetblue;
 font-weight: 800;
 font-size: 2.4rem;
 padding: 10px;
}
</style>
