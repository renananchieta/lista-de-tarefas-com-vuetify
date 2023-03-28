<template>
    <div>

        <h2>{{ tituloForm }}</h2>

        <v-col cols="12">
            <v-text-field v-if="tarefaId || tarefaId == 0" v-model="input" label="Editar Tarefa" outlined clearable
                @keyup.enter="alterarTituloDaTarefa"></v-text-field>
            <v-text-field v-else-if="!tarefaId" v-model="input" label="Nova Tarefa" outlined clearable
                @keyup.enter="addTarefa"></v-text-field>
        </v-col>
        <v-list flat subheader>
            <v-list-item-group multiple active-class="">

                <div v-for="(tarefa, index) in tarefas" :key="index">
                    <div>
                        <v-list-item :class="{ 'light-blue lighten-4': tarefa.status }"
                            @click="tarefa.status = !tarefa.status">
                            <template v-slot:default="{}">
                                <v-list-item-action>
                                    <v-checkbox @click="alterarStatusDaTarefa(tarefa)"
                                        :input-value="tarefa.status"></v-checkbox>
                                </v-list-item-action>

                                <v-list-item-content>
                                    <v-list-item-title :class="{ 'text-decoration-line-through': tarefa.status }">{{
                                        tarefa.titulo }}</v-list-item-title>
                                </v-list-item-content>

                                <v-list-item-action>
                                    <v-btn icon @click.stop="editar(tarefa.id)">
                                        Edit
                                    </v-btn>
                                    <v-btn icon @click.stop="removerTarefa(tarefa.id)">
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
            tarefaId: null,
            tarefas: [

            ]
        }
    },

    created() {
        this.carregarTarefas();
    },

    methods: {

        carregarTarefas() {
            axios.get('http://localhost:8001/api/tarefas')
                .then(response => {
                    this.tarefas = response.data;
                })
        },

        addTarefa() {
            axios.post('http://localhost:8001/api/tarefas/store', {
                titulo: this.input
            })
                .then(response => {
                    this.carregarTarefas();
                })

            this.input = '';
        },

        editar(tarefaId) {
            axios.get('http://localhost:8001/api/tarefa/' + tarefaId)
                .then(response => {
                    this.tarefa = response.data;
                    this.input = this.tarefa.titulo;
                    this.tituloForm = 'Editar';
                    this.tarefaId = this.tarefa.id;
                })
        },

        alterarTituloDaTarefa() {
            axios.post('http://localhost:8001/api/tarefa/update/titulo/' + this.tarefaId, {
                id: this.tarefaId,
                titulo: this.input
            })
                .then(response => {
                    this.carregarTarefas();
                })
                .finally(() => {
                    this.tarefaId = null,
                        this.input = '',
                        this.tituloForm = 'Cadastro';
                })
        },

        alterarStatusDaTarefa(tarefa) {
            console.log(tarefa)
            axios.post('http://localhost:8001/api/tarefa/status/update', {
                id: tarefa.id,
                status: tarefa.status
            })
                .then(response => {
                    this.carregarTarefas();
                })
        },


        removerTarefa(idTarefa) {
            this.tarefaId = idTarefa;
            if (confirm('Deseja realmente remover esta tarefa?')) {
                axios.post('http://localhost:8001/api/tarefa/delete', {
                    id: this.tarefaId
                })
                    .then(response => {
                        this.carregarTarefas();
                    })
                    .finally(() => {
                        this.tarefaId = null;
                        this.input = '',
                        this.tituloForm = 'Cadastro';
                    })
            } 
        },

        mudarStatusDaTarefa() {
            axios.post('http://localhost:8001/api/tarefa/status/update', {

            })
                .then(response => {
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