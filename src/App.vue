<script setup>
import { ref, onMounted, watch } from 'vue';
import { db } from './data/guitars';
import GuitarCard from './components/GuitarCard.vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';

const guitars = ref([]);
const cart = ref([]);
const guitar = ref({});

watch(cart, () => {
  saveCartLocalStorage();
}, { deep: true });

onMounted(() => {
  guitars.value = db;
  guitar.value = db[3];

  const cartLocalStorage = localStorage.getItem('cart');

  if (cartLocalStorage) {
    cart.value = JSON.parse(cartLocalStorage);
  }
});

const saveCartLocalStorage = () => {
  localStorage.setItem('cart', JSON.stringify(cart.value));
}

const addCart = (guitar) => {
  const guitarSearched = cart.value.find((item) => item.id === guitar.id);

  if (guitarSearched) {
    if (guitar.quantity < 5) {
      guitarSearched.quantity++;
    }
  } else {
    guitar.quantity = 1;
    cart.value.push(guitar);
  }
};

const decrementQuantity = (id) => {
  const guitar = cart.value.find((item) => item.id === id);

  if (guitar.quantity > 0) {
    guitar.quantity--;
    if (guitar.quantity === 0) {
      removeGuitar(id);
    }
  }
};

const incrementQuantity = (id) => {
  const guitar = cart.value.find((item) => item.id === id);

  if (guitar.quantity < 5) {
    guitar.quantity++;
  }
};

const removeGuitar = (id) => {
  const cartAfterRemove = cart.value.filter((item) => item.id !== id);
  cart.value = cartAfterRemove;
};

const emptyCart = () => {
  cart.value = [];
};

</script>

<template>
  <Header :cart="cart" :guitar="guitar" @add-cart="addCart" @decrement-quantity="decrementQuantity"
    @empty-cart="emptyCart" @increment-quantity="incrementQuantity" @remove-guitar="removeGuitar" />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">

      <div v-for="guitar in guitars" class="col-md-6 col-lg-4 my-4 row align-items-center">
        <GuitarCard :guitar="guitar" @add-cart="addCart" />
      </div><!-- FIN GUITARRA -->

    </div>
  </main>

  <Footer />
</template>

<style scoped></style>
