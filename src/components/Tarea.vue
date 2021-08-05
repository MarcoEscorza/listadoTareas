<template>
    <div>
        <h1 class="display-4 text-center">Listado de tareas UAEH v0.1 </h1>
        <hr>
        <div class="row">
            <div class="col-lg-8 offset-lg-2">
                <div class="card mt-4">
                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" v-model="tarea"
                            class="form-control form-control-lg" placeholder="Agregar tarea" >
                            <div class="input-group-append">
                                <button class="btn btn-success btn-lg"
                                v-on:click="agregarTarea()"
                                >Agregar</button>
                            </div>
                        </div>
                        <br>
                        <!--{{listTareas}}-->

                        <div class="text-center">
                            <div v-if="loading" class="spinner-border text-success" role="status">
                                <span class="sr-only">Loading...</span>
                            </div>
                        </div>
                        <h5 v-if="listTareas.length == 0" class="text-center text-bold">No hay tareas para realizar</h5>
                        
                        <ul class="list-group">
                            <li v-for="(tarea,index) of listTareas" :key="index"
                            class="list-group-item d-flex justify-content-between">
                                <span class="cursor" v-bind:class="{'text-success': tarea.estado}"
                                v-on:click="editarTarea(tarea,tarea.id)">
                                    <i v-bind:class="[tarea.estado ? 'fas fa-check-circle' : 'far fa-circle']"></i>
                                    
                                </span>
                                {{tarea.nombre}}
                                <span class="text-danger cursor" v-on:click="eliminarTarea(tarea.id, index)">
                                    <i class="fas fa-trash-alt"></i>
                                </span>
                            </li>
                        </ul>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>

<script>

import axios from 'axios';

    const URL = "https://backend-tareasservices.azurewebsites.net/api/Tarea/";
    export default {
       name: 'Tarea' ,
       data(){
           return{
               tarea: '',
               listTareas :[],
               loading: false
           }
       },
       methods:{
           agregarTarea(){
              
              const tarea = {
                  nombre : this.tarea,
                  estado: false
              }

            //   this.listTareas.push(tarea)
            this.loading = true;
            axios.post(URL,tarea).then(response =>{
                console.log(response.data.message)
                this.loading=false;
                this.obtenerTareas();
            }).catch(error =>{
                console.error(error);
                this.loading=false;
            })
              this.tarea = '' 
           },
           eliminarTarea(id, index){
               //this.listTareas.splice(index,1)
               axios.delete(URL+id).then(response =>{
                   console.log(response.data.message);
                   this.listTareas.splice(index,1)
               }).catch(error => console.log(error))

           },
           editarTarea(tarea, id){
            //    this.listTareas[index].estado = !tarea.estado
            this.loading = true;
                axios.put(URL+id,tarea).then(()=>{
                    this.loading = false
                    this.obtenerTareas()
                }).catch(()=>this.loading = false);
           },
           
           obtenerTareas(){
               axios.get(URL).then(response =>{
                   //console.log(response.data);
                   this.listTareas = response.data;
               })
           }
       }
       ,created: function(){
           this.obtenerTareas();

       }
    }
</script>

<style  scoped>

.cursor{
    cursor: pointer;
}

</style>