<script setup>
import { ref, computed, onMounted, watch } from 'vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';
import Guitarra from './components/Guitarra.vue';
import { db } from './data/guitarras';

const guitars = ref(db)
const carrito = ref([])

onMounted(() => {
    carrito.value = recuperaStorage()
})

function guardaStorage(){
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

function recuperaStorage(){
    const datosStorange = localStorage.getItem('carrito')
    return datosStorange ? JSON.parse(datosStorange): []
}

const total = computed(() => {
    let total = 0
    carrito.value.forEach(guitar => {
        total += guitar.cantidad * guitar.precio
    })
    return total
})

function agregarCarrito(guitar){
    //si id existe en carrito incrementamos cantidad
    const id = carrito.value.findIndex(g => g.id === guitar.id)
    if( id ==0 -1){
        carrito.value.push({ ...guitar, cantidad: 1})
}else {
carrito.value[id].cantidad++
}
    
    console.log('se agrego!', guitar.nombre)
}

function agregaUno(id){
    const idCarrito = carrito.value
        .findIndex(guitar => guitar.id === id)
    carrito.value[idCarrito].cantidad++
}

function quitaUno(id){
    const idCarrito = carrito.value
     .findIndex(guitar => guitar.id === id)
    if(carrito.value[idCarrito].cantidad > 1)
     carrito.value[idCarrito].cantidad--
    else
    quitaGuitarra(id)
}

function quitaGuitarra(id){
    carrito.value = carrito.value
     .filter(guitar => guitar.id !== id)
}

function vaciarCarrito(){
    carrito.value = []
}

watch(carrito, guardaStorage, {deep: true} )

</script>
<template>
    <Header 
    :carrito="carrito"
    :total="total"
    :guitarvai="guitars[3]"
    @agregar-carrito="agregarCarrito"
    @agrega-uno="agregaUno"
    @quita-uno="quitaUno"
    @quita-guitarra="quitaGuitarra"
    @vaciar-carrito="vaciarCarrito"

    />
    
    
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
        <Guitarra v-for="guitar in guitars"
        :key="guitar.id"
        :guitarra="guitar"
        @agregar-carrito="agregarCarrito"/>
        </div>

        <div class="row mt-5">
        <Guitarra v-for="guitar in guitars"
        :key="guitar.id"
        :guitarra="guitar"
        @agregar-carrito="agregaUno"/>
        </div>
       
    </main>

    <Footer />
</template>


