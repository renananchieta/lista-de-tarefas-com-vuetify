<template>
  <div>
    <v-col cols="12" sm="6">
      <v-text-field v-model="input" label="Nova Tarefa" outlined clearable @keyup.enter="addTarefa" ></v-text-field>
    </v-col>
    <v-list flat subheader>
      <v-list-item-group  multiple active-class="">
        <div v-for="tarefa, index in $store.state.tarefas" :key="index">
          <Tarefa :tarefa="tarefa" />
        </div>
      </v-list-item-group>
    </v-list>
  </div>
</template>

<script>
import Tarefa from '../components/tarefas/Tarefa.vue'

export default {
  name: 'Home',
  data() {
    return {
      input: null,
    }
  },
  methods:{
    addTarefa(){
      this.$store.commit('adicionarTarefa', this.input);
      this.input = null;
    }
  },

  components: {
    Tarefa
  },

}
</script>
