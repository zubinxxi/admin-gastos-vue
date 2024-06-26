<script setup>
import { ref, computed } from 'vue';
import cerrarModal from '../assets/img/cerrar.svg'

import Alerta from './Alerta.vue';

const error = ref('')

const emit = defineEmits(['cerrar-modal', 'update:nombre', 'update:cantidad', 'update:categoria', 'guardar-gasto', 'eliminar-gasto'])

const props = defineProps({
    modal: {
        type: Object,
        required: true
    },
    nombre:{
        type: String,
        required:true
    },
    cantidad:{
        type: [String, Number],
        required:true
    },
    categoria:{
        type: String,
        required:true
    },
    disponible:{
        type:Number,
        required:true
    },
    id:{
        type:[String,null],
        required:true
    }

})

const idEditing = computed(() => {
    return props.id
})

const old = props.cantidad

const agregarGasto = () =>{
    //Validar que no haya campos vacios
    const {nombre,cantidad,categoria,disponible, id} = props
    if([nombre,cantidad,categoria].includes('')){
        error.value = 'Todos los campos son obligatorios'
        setTimeout(()=>{
            error.value = '';
        }, 3000);
        return
    }

    //Validar la cantidad
    if(cantidad <=0){
        error.value = 'Cantidad no válida'
        setTimeout(()=>{
            error.value = '';
        }, 3000);
        return
    }
   
    

    //validar que el usuario no gaste mas de lo disponible
    if(id){

        // Tomar en cuenta  el gasto realizado
        if(cantidad > old + disponible){
            error.value = 'Exediste lo disponible o presupuestado'
            setTimeout(()=>{
                error.value = '';
            }, 3000);
            return
        }

        }else{

            if(cantidad > disponible){
                error.value = 'Exediste lo disponible o presupuestado'
                setTimeout(()=>{
                    error.value = '';
                }, 3000);
                return
            }

    }

    

    emit('guardar-gasto')
}

</script>

<template>
    <div class="modal">
        <div class="cerrar-modal">
            <img :src="cerrarModal" @click="$emit('cerrar-modal')">
        </div>
        <div 
            class="contenedor contenedor-formulario"
            :class="[modal.animar ? 'animar' : 'cerrar']"
        >
            <form
                class="nuevo-gasto"
                @submit.prevent="agregarGasto"
            >

                <legend>
                    {{ idEditing ? 'Actualizar Gasto' : 'Añadir Gasto'}}
                </legend>

                <Alerta v-if="error">{{ error }}</Alerta>

                <div class="campo">
                    <label for="nombre">Nombre Gasto: </label>
                    <input 
                        type="text"
                        id="nombre"
                        placeholder="Añade el nombre del gasto"
                        :value="nombre"
                        @input="$emit('update:nombre', $event.target.value)"
                    >
                </div>
                <div class="campo">
                    <label for="cantidad">Cantidad: </label>
                    <input 
                        type="number"
                        id="cantidad"
                        placeholder="Añade la cantidad del gasto, ej. 300"
                        :value="cantidad"
                        @input="$emit('update:cantidad', +$event.target.value)"
                    >
                </div>

                <div class="campo">
                    <label for="categoria">Categoría: </label>
                    <select 
                        id="categoria"
                        :value="categoria"
                        @input="$emit('update:categoria', $event.target.value)"
                    >
                        <option value="">-- Seleccionar --</option>
                        <option value="ahorro">Ahorro</option>
                        <option value="comida">Comida</option>
                        <option value="casa">Casa</option>
                        <option value="gastos">Gastos Varios</option>
                        <option value="ocio">Ocio</option>
                        <option value="salud">Salud</option>
                        <option value="suscripciones">Suscripciones</option>

                    </select>
                </div>

                <input 
                    type="submit" 
                    :value="[idEditing ? 'Guardar Gasto' : 'Añadir Gasto']"
                >

            </form>
            <button
                type="button"
                class="btn-eliminar"
                v-if="idEditing"
                @click="$emit('eliminar-gasto', id)"
            >
                Eliminar Gasto
            </button>
        </div>
    </div>
</template>

<style scoped>
    .modal{
        position: absolute;
        background-color: rgb(0 0 0 / 0.9 );
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
    }

    .cerrar-modal{
        position: absolute;
        right: 3rem;
        top: 3rem;
    }

    .cerrar-modal img{
        width: 3rem;
        cursor: pointer;
    }

    .nuevo-gasto{
        margin: 10rem auto 0 auto;
        display: grid;
        gap: 2rem;
    }

    .campo{
        display: grid;
        gap: 2rem;
    }

    .nuevo-gasto input,
    .nuevo-gasto select{
        background-color: var(--gris-claro);
        border-radius: 1rem;
        padding: 1rem;
        border: none;
        font-size: 2.2rem;
    }

    .nuevo-gasto label{
        color: var(--negro);
        font-size: 3rem;
    }

    .nuevo-gasto legend{
        color: var(--azul);
        margin-top: 2rem;
        font-weight: 900;
        font-size: 5rem;
        text-align: center;
    }

    .contenedor-formulario{
        background-color: var(--blanco);
        border-radius: 1rem;
        padding: 3rem;
        padding-top: 0;
        margin-top: 3rem ;
        transition-property: all;
        transition-duration: 300ms;
        transition-timing-function: ease-in;
        opacity: 0;
    }

    .contenedor-formulario.animar{
        opacity: 1;
    }

    .contenedor-formulario.cerrar{
        opacity: 0;
    }

    .nuevo-gasto input[type="submit"]{
        background-color: var(--azul);
        border-radius: 0;
        border: none;
        padding: 1rem;
        text-align: center;
        font-size: 2rem;
        margin-top: 2rem;
        color: var(--blanco);
        font-weight: 900;
        width: 100%;
        transition: background-color 300ms ease;
    }
    .nuevo-gasto input[type="submit"]:hover{
        background-color: var(--azul-oscuro);
        cursor: pointer;
    }

    .btn-eliminar{
        padding: 1rem;
        width: 100%;
        background-color: var(--rojo-boton);
        font-weight: 900;
        font-size: 2rem;
        color: var(--blanco);
        margin-top: 5rem;
        cursor: pointer;
        border: none;
    }

    .btn-eliminar:hover{
        background-color: var(--rojo-boton-hover);
    }
</style>