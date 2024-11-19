<template>
  <div class="d-flex flex-column align-items-center justify-content-center min-vh-100">
    <h3 class="text-white">Carrinho de Compras</h3>

    <div
      v-for="product in visibleProducts"
      :key="product.id"
      class="card mb-3"
      style="width: 70rem; height: 12rem; color: white; background-color: #2C2C36;"
    >
      <div class="card-body">
        <p class="card-text">{{ product.name }}</p>
        <span class="stars">{{ product.rating }} ⭐ ({{ product.reviews }} avaliações)</span>
        <h3 style="color: #A33AFF;">R$ {{ product.price.toFixed(2) }}</h3>
        <button
          @click="addToCart(product)"
          class="btn"
          style="background-color: #39115C; color: white; width: 15rem;"
        >
          Adicionar ao carrinho
        </button>
      </div>
    </div>

    <div class="button-container mt-4">
      <button
        v-if="productsToShow < products.length"
        @click="loadMore"
        class="btn btn-primary"
        style="background-color: #39115C; color: white; border: none;"
      >
        Carregar mais produtos
      </button>
      <router-link
        to="/cart"
        class="btn btn-success ms-3"
        style="background-color: #39CC33; color: white; border: none;"
      >
        Ir para o carrinho
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      productsToShow: 3,
      cart: [],
    };
  },
  created() {
    this.loadCart();
    this.fetchProducts();
  },
  methods: {
    fetchProducts() {
      fetch('http://localhost:4000/products')
        .then((response) => response.json())
        .then((data) => {
          this.products = data;
        });
    },
    loadMore() {
      this.productsToShow += 3;
    },
    addToCart(product) {
      const productInCart = this.cart.find((item) => item.id === product.id);
      if (!productInCart) {
        this.cart.push({ ...product, quantity: 1 });
        this.updateLocalStorage();
        alert('Produto adicionado ao carrinho!');
      }
    },
    loadCart() {
      const storedCart = localStorage.getItem('cart');
      if (storedCart) {
        this.cart = JSON.parse(storedCart);
      }
    },
    updateLocalStorage() {
      localStorage.setItem('cart', JSON.stringify(this.cart));
    },
  },
  computed: {
    visibleProducts() {
      return this.products.slice(0, this.productsToShow);
    },
  },
};
</script>

<style>
  body{
    background-color: #14151D;
  }
</style>