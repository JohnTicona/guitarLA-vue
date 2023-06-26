<script setup lang="ts">
import Header from "./components/Header.vue";
import Guitarra from "./components/Guitarra.vue";
import Footer from "./components/Footer.vue";
import { db } from "./data/guitarras";
import { onMounted, ref, watch } from "vue";
import { IGuitarra, IProducto } from "./interfaces";

const guitarras = ref<IGuitarra[]>([]);
const carrito = ref<IProducto[]>([]);
const guitarra = ref<IGuitarra>({} as IGuitarra);

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];
  carrito.value = JSON.parse(localStorage.getItem("carrito") || "[]");
});

watch(
  carrito,
  (newValue) => {
    localStorage.setItem("carrito", JSON.stringify(newValue));
  },
  { deep: true }
);

const agregarProducto = (producto: IGuitarra) => {
  const existeProducto = carrito.value.findIndex(
    (productoCarrito) => productoCarrito.id === producto.id
  );
  if (existeProducto >= 0) {
    carrito.value[existeProducto].cantidad++;
  } else {
    carrito.value.push({ ...producto, cantidad: 1 });
  }
};

const eliminarProducto = (productoId: number) => {
  carrito.value = carrito.value.filter(
    (producto) => producto.id !== productoId
  );
};

const vaciarCarrito = () => {
  carrito.value = [];
};

const incrementarCantidad = (id: number) => {
  const index = carrito.value.findIndex(
    (productoCarrito) => productoCarrito.id === id
  );
  carrito.value[index].cantidad++;
};

const decrementarCantidad = (id: number) => {
  const index = carrito.value.findIndex(
    (productoCarrito) => productoCarrito.id === id
  );
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @vaciar-carrito="vaciarCarrito"
    @eliminar-producto="eliminarProducto"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @agregar-producto="agregarProducto"
  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        :guitarra="guitarra"
        @agregar-producto="agregarProducto"
      />
    </div>
  </main>
  <Footer />
</template>
