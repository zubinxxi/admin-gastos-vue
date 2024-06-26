<script setup>
//import imagen from '../assets/img/grafico.jpg'
import CircleProgress from 'vue3-circle-progress'
import "vue3-circle-progress/dist/circle-progress.css"
import { formatearCantidad } from '../helpers';
import { computed } from 'vue';

const props = defineProps({
    presupuesto: {
        type: Number,
        required: true
    },
    disponible: {
        type: Number,
        required: true
    },
    gastado:{
        type:Number,
        required:true
    }
})

defineEmits(['reset-app'])

const porcentaje = computed(() =>{
   return parseInt(((props.presupuesto - props.disponible) / props.presupuesto) * 100)
})

</script>

<template>
    <div class="dos-columnas">
        <div class="contenedor-grafico">
            <p class="porcentaje">{{ porcentaje }}%</p>
            <!--<img :src="imagen" />-->
            <CircleProgress
                :percent="porcentaje"
                :size="250"
                :border-width="30"
                :border-bg-width="30"
                fill-color="var(--azul)" 
                empty-color="var(--gris-claro)"
            />
        </div>
        <div class="contenedor-presupuesto">
            <button 
                type="button"
                class="reset-app"
                @click="$emit('reset-app')"
            >
                Resetear App
            </button>
            <p>
                <span for="">Presupuesto</span>
                {{ formatearCantidad(presupuesto) }}
            </p>
            <p>
                <span for="">Disponible</span>
                {{ formatearCantidad(disponible) }}
            </p>
            <p>
                <span for="">Gastado</span>
                {{ formatearCantidad(gastado) }}
            </p>
        </div>
    </div>
</template>

<style scoped>
    .dos-columnas{
        display: flex;
        flex-direction: column;
    }

    .dos-columnas > :first-child{
        margin-bottom: 3rem;
    }

    @media (min-width: 768px){
        .dos-columnas{
            flex-direction: row;
            gap: 4rem;
            align-items: center;
        }

        .dos-columnas > :first-child{
            margin-bottom: 0;
        }
    }

    .reset-app{
        background-color: var(--rojo-boton);
        border: none;
        padding: 1rem;
        width: 100%;
        color: var(--blanco);
        font-weight: 900;
        text-transform: uppercase;
        border-radius: 1rem;
        transition-property: background-color;
        transition-duration: 300ms;
    }

    .reset-app:hover{
        cursor: pointer;
        background-color: var(--rojo-boton-hover);
    }

    .contenedor-presupuesto{
        width: 100%;

    }

    .contenedor-presupuesto p{
        font-size: 2.4rem;
        text-align: center;
        color: var(--gris-oscuro);
    }

    .contenedor-grafico {
        position: relative;
    }
    
    .porcentaje{
        position: absolute;
        margin: auto;
        top: calc(50% - 1.5rem);
        left: 0;
        right: 0;
        text-align: center;
    }

    @media (min-width: 768px){
        .contenedor-presupuesto p{
            text-align: left;
        }
    }

    .contenedor-presupuesto span{
        font-weight: 900;
        color: var(--azul);
    }

    .contenedor-grafico {
        position: relative;
    }
    
    .porcentaje{
        position: absolute;
        margin: auto;
        top: calc(50% - 1.5rem);
        left: 0;
        right: 0;
        text-align: center;
        font-size: 3rem;
        font-weight: 900;
        color: var(--gris-oscuro);
        z-index: 100;
    }

    
</style>