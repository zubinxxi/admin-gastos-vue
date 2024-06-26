<script setup>
import { ref, reactive, watch, computed, onMounted } from 'vue';
import Presupuesto from './components/Presupuesto.vue';
import controlPresupuesto from './components/controlPresupuesto.vue';
import Modal from './components/Modal.vue'
import Gasto from './components/Gasto.vue'
import Filtros from './components/Filtros.vue'

import inconoNuevoGasto from './assets/img/nuevo-gasto.svg'

import {generarId} from './helpers/'


const presupuesto = ref(0)
const disponible = ref(0)
const gastado = ref(0)
const filtro = ref('')

const modal = reactive({
  mostrar: false,
  animar: false,

})

const gasto = reactive({
  nombre:'',
  cantidad:'',
  categoria:'',
  id:null,
  fecha:Date.now()
})

const gastos = ref([])

watch(gastos, () =>{
  // Almacenando los gastos en localStorage
  gastosEnLocalStorage()

  const totalGastado = gastos.value.reduce((total, gasto) => gasto.cantidad + total, 0)
  gastado.value = totalGastado
  disponible.value = presupuesto.value - gastado.value
},{
    deep:true
})

watch(modal, () =>{
  if(!modal.mostrar){
    //Reiniciar modal
    Object.assign(gasto, {
      nombre:'',
      cantidad:'',
      categoria:'',
      id:null,
      fecha:Date.now()
    })
  }
}, {
  deep:true
})


watch(presupuesto, () =>{

  localStorage.setItem('presupuesto', presupuesto.value)
  
})


onMounted(() =>{
  const presupuestoStorage = localStorage.getItem('presupuesto')
  const gastosStorage = localStorage.getItem('gastos')
  if(presupuestoStorage){
    presupuesto.value = Number(presupuestoStorage)
    disponible.value = Number(presupuestoStorage)
    gastos.value = JSON.parse(gastosStorage)
  }
})

const definirPresupuesto = (cantidad) =>{
  presupuesto.value = cantidad
  disponible.value = cantidad
}

const mostrarModal = () => {
  modal.mostrar = true
  setTimeout(() =>{
    modal.animar = true
  }, 300);
  
}

const cerrarModal = () => {
  modal.animar = false
  setTimeout(() =>{
    modal.mostrar = false
  }, 300);
  
}

const guardarGasto = () =>{
  if(gasto.id){

    // Editando
    const {id} = gasto
    const i = gastos.value.findIndex((gasto => gasto.id === id))

    // Actualizando el gasto en el arreglo
    gastos.value[i] = {...gasto}

  }else{

    // Registro nuevo
    gastos.value.push({
      ...gasto,
      id:generarId() 
    })
  }
  

  cerrarModal()

  //Reiniciar modal
  Object.assign(gasto, {
    nombre:'',
    cantidad:'',
    categoria:'',
    id:null,
    fecha:Date.now()
  })

}

const seleccionarGasto = (id) =>{
  const gastoEditar = gastos.value.filter(gasto => gasto.id === id)[0]
  Object.assign(gasto, gastoEditar)
  mostrarModal()

}

const eliminarGasto = (id) =>{
  if(confirm('Eliminar?')){
    gastos.value = gastos.value.filter(gasto => gasto.id != id)
    cerrarModal()
  }
  
}

const gastosFiltrados = computed(() =>{
  if(filtro.value){
    return gastos.value.filter(gasto => gasto.categoria === filtro.value)
  }

  return gastos.value
})

//Funcion que agrega los gastos al localStorage
const gastosEnLocalStorage = () =>{
  localStorage.setItem('gastos', JSON.stringify(gastos.value))
}

const resetApp = () =>{
  if(confirm('Deseas reiniciar presupuesto y gastos?')){
    gastos.value = []
    presupuesto.value = 0
  }
}
</script>

<template>
  <div
    :class="{fijar: modal.mostrar}"
  >
    <header>
      <h1>Planificador de Gastos</h1>
      <div class="contenedor-header contenedor sombra">
        <Presupuesto
          v-if="presupuesto === 0"
          @definir-presupuesto="definirPresupuesto"
        />
        
        <controlPresupuesto
          v-else
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
          @reset-app="resetApp"
        />
        
      </div>
    </header>
    <main v-if="presupuesto > 0">

      <Filtros 
        v-model:filtro="filtro"
      />

      <div class="listado-gastos contenedor">
        <h2>{{gastosFiltrados > 0 ? 'Gastos' : 'No hay gastos'}}</h2>
        <Gasto 
          v-for="gasto in gastosFiltrados"
          :key="gasto.id"
          :gasto="gasto"
          @seleccionar-gasto="seleccionarGasto"
        />
      </div>

      <div class="crear-gasto">
        <img 
          :src="inconoNuevoGasto"
          alt="icono nuevo gasto"
          @click="mostrarModal"
        />
      </div>
      <Modal 
        v-if="modal.mostrar"
        :modal="modal"
        :disponible="disponible"
        :id="gasto.id"
        v-model:nombre="gasto.nombre"
        v-model:cantidad="gasto.cantidad"
        v-model:categoria="gasto.categoria"
        @cerrar-modal="cerrarModal"
        @guardar-gasto="guardarGasto"
        @eliminar-gasto="eliminarGasto"
      />
    </main>
  </div>
</template>

<style>
  :root{
    --azul: #3b82f6;
    --azul-oscuro: #1048a4;
    --rojo: #b91c1c;
    --rojo-boton: #db2777;
    --rojo-boton-hover: #c11d67;
    --blanco: #fff;
    --gris-claro: #f5f5f5;
    --gris: #94a3b8;
    --gris-oscuro: #64748b;
    --negro: #000;
  }

  html{
    font-size: 62.5%;
    box-sizing: border-box;
  }
  *,
  *:before,
  *:after{
    box-sizing: inherit;
  }

  body{
    font-size: 1.6rem;
    font-family: "Lato", sans-serif;
    background-color: var(--gris-claro);
  }

  h1{
    font-size: 4rem;
  }

  h2{
    font-size: 3rem;
  }

  .fijar{
    overflow: hidden;
    height: 100vh;
  }

  header{
    background-color: var(--azul);
  }

  header h1{
    padding: 3rem 0;
    margin: 0;
    color: var(--blanco);
    text-align: center;
  }

  .contenedor{
    width: 90%;
    max-width: 80rem;
    margin: 0 auto;
  }

  .contenedor-header{
    margin-top: -5rem;
    transform: translateY(5rem);
    padding: 5rem;
  }

  .sombra{
    box-shadow: 0px 10px 15px -3px rgba(0,0,0,0.1);
    background-color: var(--blanco);
    border-radius: 1.2rem;
    padding: 5rem;
  }

  .crear-gasto{
    position: fixed;
    bottom: 5rem;
    right: 5rem;
  }

  .crear-gasto img{
    width: 5rem;
    cursor: pointer;
  }

  .listado-gastos{
    margin-top: 10rem;

  }
  
  .listado-gastos h2{
    font-weight: 900;
    color: var(--gris-oscuro);
  }


</style>
