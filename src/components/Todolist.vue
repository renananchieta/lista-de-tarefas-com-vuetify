<template>
    <div>
    
        <h2>{{ tituloForm }}</h2>

        <v-col cols="12">
            <v-text-field v-if="indiceEditado || indiceEditado == 0" v-model="input" label="Editar Tarefa" outlined clearable @keyup.enter="alterarTarefa" ></v-text-field>
            <v-text-field v-else-if="!indiceEditado" v-model="input" label="Nova Tarefa" outlined clearable @keyup.enter="addTarefa" ></v-text-field>
        </v-col>
        <v-list flat subheader>
            <v-list-item-group  multiple active-class="">
    
            <div v-for="(tarefa, index) in tarefas" :key="index">
            
                <div>
                    <v-list-item :class="{ 'light-blue lighten-4': tarefa.status }" @click="mudarStatusDaTarefa(tarefa.status = !tarefa.status)">
                        <template v-slot:default="{}">
                            <v-list-item-action>
                                <v-checkbox :input-value="tarefa.status"></v-checkbox>
                            </v-list-item-action>

                            <v-list-item-content>
                                <v-list-item-title
                                    :class="{ 'text-decoration-line-through': tarefa.status }">{{ tarefa.titulo }}</v-list-item-title>
                            </v-list-item-content>

                            <v-list-item-action>
                                <v-btn icon @click.stop="editar(index, tarefa.titulo)">
                                    Edit
                                </v-btn>
                                <v-btn icon @click.stop="removerTarefa(index)">
                                    <v-icon color="red lighten-1">mdi-delete-circle</v-icon>
                                </v-btn>
                            </v-list-item-action>

                        </template>
                    </v-list-item>
                    <v-divider></v-divider>
                </div>
            
          </div>
  
        </v-list-item-group>
      </v-list>
    </div>
    
</template>

<script>

    const axios = require('axios').default;

export default {
    data() {
        return {
            tituloForm: 'Cadastro',
            input: '',
            indiceEditado: null,
            tarefas: [

            ]
        }
    },

    created() {
        
        this.carregarTarefas();

         //let storage = localStorage.getItem('tarefas');
         //if(storage) {
           //this.tarefas = JSON.parse(storage);
         //}

    },

    methods:{

        carregarTarefas(){
            axios.get('http://localhost:8001/api/tarefas')
                .then(response => {
                    this.tarefas = response.data;
                })
        },

        
        
        addTarefa(){
            axios.post('http://localhost:8001/api/tarefas/store', {
                titulo: this.input
            }).then(response =>{
                alert('Tarefa registrada com sucesso.');
                this.carregarTarefas();
            })
        },

        alterarTarefa() {
            this.tarefas[this.indiceEditado] = {
                id: this.indiceEditado,
                titulo: this.input,
                status: false
            };

            this.input = null;
            this.indiceEditado = '';
            this.tituloForm = 'Cadastro';

            //this.salvarNoLocalStorage();
        },

        editar(i, titulo) {
            this.input = titulo;

            this.indiceEditado = i;

            this.tituloForm = 'Editar';
        },

        removerTarefa(indice){
            this.tarefas.splice(indice, 1);
            
             //this.salvarNoLocalStorage();
        },

        mudarStatusDaTarefa(){
            axios.post('http://localhost:8001/api/tarefa/status/update', {
                id: this.tarefa.id,
                status: this.tarefa.status
            })
                .then(response =>{
                    this.carregarTarefas();
                })
        }

        //salvarNoLocalStorage() {
            //localStorage.setItem('tarefas', JSON.stringify(this.tarefas));
        //}
    }
}
</script>

<style></style>