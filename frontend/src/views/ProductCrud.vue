<template>
    <div class="py-2 flex flex-row m-4 justify-start">
        <button class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded" @click="visible = true; limpiar();">
            Nou Producte
        </button>
    </div>
    
    <div class="w-full px-4">
        <DataTable :value="productos" paginator :rows="5" :rowsPerPageOptions="[5, 10, 20, 50]" class="my-2">
            <Column field="descripcion" header="Descripció"></Column>
            <Column field="codigo_producto" header="Codi Producte"></Column>
            <Column field="activo" header="Actiu"></Column>
            <Column field="fecha_entrada" header="Data Entrada"></Column>
            <Column header="Característica 1">
                <template #body="rowData" >
                    <span v-if="rowData.data.pc_sobremesa">Gaming:  {{ rowData.data.pc_sobremesa.gaming }}</span>
                    <span v-else>Capacitat Bateria:  {{ rowData.data.portatil.capacidad_bateria }}</span>
                </template>
            </Column>
            <Column header="Característica 2">
                <template #body="rowData" >
                    <span v-if="rowData.data.pc_sobremesa">Potència Font:  {{ rowData.data.pc_sobremesa.potencia_fuente }}</span>
                    <span v-else>Amperatge Carregador:  {{ rowData.data.portatil.amperaje_cargador }}</span>
                </template>
            </Column>

            <Column header="Editar">
                <template #body="rowData">
                    <div class="w-full flex justify-around items-center">
                        <Button icon="pi pi-pencil" severity="warning" rounded @click="editar(rowData)" class="bg-orange-400 hover:bg-orange-600 border-orange-600"></Button>
                    </div>
                </template>
            </Column>
        </DataTable>
    </div>
    

    <Dialog v-model:visible="visible" modal header="Producte" :style="{ width: '80vw' }">
        <form class=" px-8 pb-8 mb-4">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="descripcion">
                    Descripció
                </label>
                <input v-model="producto.descripcion" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="descripcion" type="text" >
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="codigo_producto">
                    Codi
                </label>
                <input v-model="producto.codigo_producto" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="codigo_producto" type="text" >
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="activo">
                    Actiu
                </label>
                <InputSwitch id="activo" v-model="producto.activo" />
            </div>
            <div class="mb-4" v-if="producto.id==0">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="tipo_producto">
                    Tipus
                </label>
                <select id="tipo_producto" v-model="producto.tipo_producto" name="tipo_producto" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-400">
                    <option value="pc_sobremesa"> PC de sobre taula</option>
                    <option value="portatil">Portàtil</option>
                </select>
            </div>

            <div v-if="producto.tipo_producto == 'pc_sobremesa'">
                <div class="mb-4">
                    <label class="block text-gray-700 text-sm font-bold mb-2" for="Gaming">
                        Gaming
                    </label>
                    <InputSwitch id="Gaming" v-model="producto.gaming" />
                    
                </div>
                <div class="mb-4">
                    <label class="block text-gray-700 text-sm font-bold mb-2" for="potencia_fuente">
                        Potència Font
                    </label>
                   
                    <input v-model="producto.potencia_fuente" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="potencia_fuente" type="number" />
                </div>

            </div>
         

            <div v-else>
                <div class="mb-4">
                    <label class="block text-gray-700 text-sm font-bold mb-2" for="Bateria">
                        Capacitat Bateria
                    </label>
                    
                    <input v-model="producto.capacidad_bateria" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="Bateria" type="number" />
                </div>
                <div class="mb-4">
                    <label class="block text-gray-700 text-sm font-bold mb-2" for="amperaje_cargador">
                        Amperatge Cargador
                    </label>
                    <input v-model="producto.amperaje_cargador" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="amperaje_cargador" type="number" />
                    
               
                </div>

            </div>


            <div class="flex items-center justify-between">
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="crear()" v-if="producto.id==0">Crear</button>
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button" @click="update()" v-else>Guardar</button>
            </div>
        </form>

    </Dialog>
</template>

<script>
import InputNumber from 'primevue/inputnumber';
import InputSwitch from 'primevue/inputswitch';
import Dialog from 'primevue/dialog';
import Button from 'primevue/button';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import ColumnGroup from 'primevue/columngroup';   // optional
import Row from 'primevue/row';   
import axios from 'axios';
import {mapState} from 'vuex'
import Swal from 'sweetalert2';
export default {
    components: {DataTable,Column,ColumnGroup,Row,Button,Dialog,InputSwitch,InputNumber},
    computed:{
        ...mapState(['url_api'])
    },
    data() {
        return {
            productos : [],
            producto: {
                id:0,
                descripcion:'',
                codigo_producto:'',
                activo:false,
                tipo_producto:'pc_sobremesa',
                gaming: false,
                potencia_fuente:0,
                capacidad_bateria:0,
                amperaje_cargador:0
            },
            visible:false,
        }
    },
    methods: {
        async listar(){

            try{
                let productos = await axios.get(this.url_api+'/product/getAllProducts')
                if(productos.status == 200){
                    this.productos = productos.data;
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
            let producto;
            if(data.data.pc_sobremesa != null){
                producto = {
                id:data.data.id,
                descripcion:data.data.descripcion,
                codigo_producto:data.data.codigo_producto,
                activo:data.data.activo,
                tipo_producto: 'pc_sobremesa',
                gaming: data.data.pc_sobremesa.gaming,
                potencia_fuente:data.data.pc_sobremesa.potencia_fuente,
                capacidad_bateria:0,
                amperaje_cargador:0
                }
            }else{
                producto = {
                id:data.data.id,
                descripcion:data.data.descripcion,
                codigo_producto:data.data.codigo_producto,
                activo:data.data.activo,
                tipo_producto: 'portatil',
                gaming: false,
                potencia_fuente:0,
                capacidad_bateria:data.data.portatil.capacidad_bateria,
                amperaje_cargador:data.data.portatil.amperaje_cargador
                }
            }

            this.visible = true;
            this.producto = producto;
            
        },
        limpiar(){
            this.producto = {
                id:0,
                descripcion:'',
                codigo_producto:'',
                activo:false,
                tipo_producto:'pc_sobremesa',
                gaming: false,
                potencia_fuente:0,
                capacidad_bateria:0,
                amperaje_cargador:0
            }
        },
        async update(){
            

            try{
                let producte = await axios.put(this.url_api+'/product/updateProduct',this.producto);
                if(producte.status == 200){
                    this.limpiar();
                    this.listar();
                    this.visible = false;
                    Swal.fire({
                        title: 'Producte actualitzat',
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
                let producte = await axios.post(this.url_api+'/product/newProduct',this.producto);
                if(producte.status == 200){
                    this.limpiar();
                    this.listar();
                    this.visible = false;
                    Swal.fire({
                        title: 'Producte creat',
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