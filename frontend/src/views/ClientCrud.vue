<template>
    <div class="py-2 flex flex-row m-4 justify-start">
        <button class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded" @click="visible = true; limpiar();">
            Nou Client
        </button>
    </div>
    
    <div class="w-full px-4">
        <DataTable :value="clientes" paginator :rows="5" :rowsPerPageOptions="[5, 10, 20, 50]" class="my-2">
            <Column field="nom" header="Nom"></Column>
            <Column field="cognoms" header="Cognoms"></Column>
            <Column field="email" header="Email"></Column>
            <Column field="telefon" header="Telèfon"></Column>
            <Column header="Editar">
                <template #body="rowData">
                    <div class="w-full flex justify-around items-center">
                        <Button icon="pi pi-pencil" severity="warning" rounded @click="editar(rowData)" class="bg-orange-400 hover:bg-orange-600 border-orange-600"></Button>
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>
    

    <Dialog v-model:visible="visible" modal header="Cliente" :style="{ width: '80vw' }">
        <form class=" px-8 pb-8 mb-4">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="nom">
                Nom
                </label>
                <input v-model="cliente.nom" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="nom" type="text" >
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="cognoms">
                Cognoms
                </label>
                <input v-model="cliente.cognoms" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="cognoms" type="text" >
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="email">
                Email
                </label>
                <input v-model="cliente.email" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="email" type="email" >
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="telefon">
                Telèfon
                </label>
                <input v-model="cliente.telefon" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="telefon" type="tel" >
            </div>
            <div class="flex items-center justify-between">
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="crear()" v-if="cliente.id==0">Crear</button>
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="update()" v-else>Guardar</button>
            </div>
        </form>

    </Dialog>
</template>

<script>
import Dialog from 'primevue/dialog';
import Button from 'primevue/button';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import ColumnGroup from 'primevue/columngroup';   // optional
import Row from 'primevue/row';   
import axios from 'axios';
import Swal from 'sweetalert2';
import {mapState} from 'vuex'
export default {
    components: {DataTable,Column,ColumnGroup,Row,Button,Dialog},
    computed:{
        ...mapState(['url_api'])
    },
    data() {
        return {
            clientes : [],
            cliente: {
                id:0,
                nom:'',
                cognoms:'',
                email:'',
                telefon:''
            },
            visible:false,
        }
    },
    methods: {
        async listar(){
            try{
                let clientes = await axios.get(this.url_api+'/client/getAllClients')
                console.log(clientes);
                if(clientes.status == 200){
                    this.clientes = clientes.data;
                }else{
                    Swal.fire({
                        title: 'Error!',
                        text: 'Error amb al llistat',
                        icon: 'error',
                        confirmButtonText: 'Acepta'
                    })
                }
            }catch(e){
                Swal.fire({
                    title: 'Error!',
                    text: e.message,
                    icon: 'error',
                    confirmButtonText: 'Acepta'
                })
            }

            
        },
        editar(data){
            this.limpiar();
            this.cliente = {
                id:data.data.id,
                nom:data.data.nom,
                cognoms:data.data.cognoms,
                email:data.data.email,
                telefon:data.data.telefon
            }
            this.visible = true;
        },
        limpiar(){
            this.cliente = {
                id:0,
                nom:'',
                cognoms:'',
                email:'',
                telefon:''
            }
        },
        async update(){
            


            try{
                let cliente = await axios.put(this.url_api+'/client/updateClient',this.cliente);
                console.log(cliente);
                if(cliente.status == 200){
                    this.limpiar();
                    this.listar();
                    this.visible = false;
                    Swal.fire({
                        title: 'Client actualitzat',
                        icon: 'success',
                        confirmButtonText: 'Acepta'
                    })
                }

            }catch(e){
                Swal.fire({
                    title: 'Error!',
                    text: e.response.data.message,
                    icon: 'error',
                    confirmButtonText: 'Acepta'
                })
            }
        },
        async crear(){
        

            try{
                let cliente = await axios.post(this.url_api+'/client/newClient',this.cliente);
                console.log(cliente);
                if(cliente.status == 200){
                    this.limpiar();
                    this.listar();
                    this.visible = false;
                    Swal.fire({
                        title: 'Client creat',
                        icon: 'success',
                        confirmButtonText: 'Acepta'
                    })
                }
                
            }catch(e){
                Swal.fire({
                    title: 'Error!',
                    text: e.response.data.message,
                    icon: 'error',
                    confirmButtonText: 'Acepta'
                })
            }
        }
        
    },
    async mounted() {

        await this.listar();
    },
}
</script>
