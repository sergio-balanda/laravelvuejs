<template>
    <div>

        <!-- crear -->
        <form @submit.prevent="editarNota(nota)" class="mb-2" v-if="editar">
            <div class="form-group">
                    <h3>EDITAR TAREA</h3>
            </div>
            <div class="form-group">
                <input type="text" v-model="nota.nombre" name="nombre" class="form-control" placeholder="Nombre">
            </div>
            <div class="form-group">
                <input type="text"  v-model="nota.descripcion" name="descripcion" class="form-control" placeholder="Descripcion">
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-info">editar</button>
                <button type="submit" class="btn btn-danger" @click="cancelarEdicion">cancelar</button>
            </div>
        </form>
       
        <!-- editar -->
        <form @submit.prevent="agregar" class="mb-2" v-else>
            <div class="form-group">
                <h3>AGREGAR TAREA</h3>
            </div>
            <div class="form-group">
                <input type="text" v-model="nota.nombre" name="nombre" class="form-control" placeholder="Nombre">
            </div>
            <div class="form-group">
                <input type="text"  v-model="nota.descripcion" name="descripcion" class="form-control" placeholder="Descripcion">
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-primary">enviar</button>
            </div>
        </form>

        <!-- listar -->
        <hr>
        <h3>Mis notas</h3>
        <ul class="list-group">
            <li class="list-group-item"
                v-for="(item, index) in notas" :key="index"
            >
            <span class="badge badge-primary float-right">{{ item.updated_at }}</span>
            <p>{{ item.nombre }}</p>
            <p>{{ item.descripcion }}</p>
            <button @click="eliminarNota(item, index)" class="btn btn-danger btn-sm">eliminar</button>
            <button @click="editarFormulario(item)"
                class="btn btn-sm btn-info"
            >
                Editar
            </button>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    data(){
        return{
            notas:[],
            nota: {nombre: '', descripcion: ''},
            editar: false,
        }
    },//data
    created(){
        axios.get('/notas')
            .then(res=>{
                this.notas=res.data;
            })
    },//created
    methods:{
        agregar(){
            if(this.nota.nombre.trim() === '' || this.nota.descripcion.trim() === ''){
                alert('Debes completar todos los campos antes de guardar');
                return;
            }
            //console.log(this.nota.nombre, this.nota.descripcion);
            const params = {
                nombre: this.nota.nombre, 
                descripcion: this.nota.descripcion
            };
            this.nota.nombre='';
            this.nota.descripcion='';
            axios.post('/notas',params)
                .then(res=>{
                    this.notas.push(res.data)
                })
        },
        editarFormulario(item){
            this.editar = true;
            this.nota.nombre=item.nombre;
            this.nota.descripcion=item.descripcion;
            this.nota.id = item.id;
        },
        editarNota(item){
            const params = {nombre: item.nombre, descripcion: item.descripcion};
            axios.put(`/notas/${item.id}`, params)
                .then(res=>{
                    const index = this.notas.findIndex(
                        notaBuscar => notaBuscar.id === res.data.id)
                        this.notas[index] = res.data;
                        this.editar=false;
                        this.nota = {nombre: '', descripcion:''};
                        axios.get('/notas')
                        .then(res=>{
                            this.notas=res.data;
                        });
                })
        },
        eliminarNota(item, index){
            axios.delete(`/notas/${item.id}`)
                .then(()=>{
                    this.notas.splice(index, 1);
                })
        },
        cancelarEdicion(){
            this.editar=false;
            this.nota = {nombre: '', descripcion:''};
        }
    }//methods
}
</script>