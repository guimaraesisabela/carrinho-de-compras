<template>
  <div class="container mt-5">
    <h1 class="text-white">Carrinho de Compras</h1>
    <ul v-if="cart.length">
      <li v-for="item in cart" :key="item.id" class="cart-item">
        <div>
          <h3>{{ item.name }}</h3>
          <p>R$ {{ item.price.toFixed(2) }}</p>
        </div>
        <div class="quantity-control">
          <button class="btn-quantity" @click="decreaseQuantity(item)">-</button>
          <span>{{ item.quantity }}</span>
          <button class="btn-quantity" @click="increaseQuantity(item)">+</button>
        </div>
        <h4>Subtotal: <br>
           R$ {{ (item.price * item.quantity).toFixed(2) }} 
        </h4>
      </li>
    </ul>
    <div v-else>
      <p class="text-white">Você ainda não adicionou nenhum produto ao carrinho!</p>
    </div>

    <div class="cart-total">
      <h2>Total: R$ {{ totalPrice.toFixed(2) }}</h2>
      <div class="buttons">
        <button @click="clearCart" class="btn btn-danger">Limpar Carrinho</button>
        <button @click="goBack" class="btn btn-primary">Voltar para listagem</button>
      </div>
    </div>

    <div class="modal" tabindex="-1" v-if="showModal" @click.self="closeModal">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Aviso!</h5>
            <button type="button" class="btn-close" @click="closeModal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <p>Você está prestes a remover esse item do carrinho. Tem certeza que deseja prosseguir?</p>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-danger" @click="closeModal">Não</button>
            <button type="button" class="btn btn-success" @click="confirmRemoval">Sim</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cart: [],
      showModal: false, 
      itemToRemove: null, 
    };
  },
  computed: {
    totalPrice() {
      return this.cart.reduce((total, item) => total + item.price * item.quantity, 0);
    },
  },
  created() {
    this.loadCart();
  },
  methods: {
    loadCart() {
      const storedCart = localStorage.getItem('cart');
      if (storedCart) {
        this.cart = JSON.parse(storedCart);
      }
    },
    increaseQuantity(item) {
      item.quantity++;
      this.updateLocalStorage();
    },
    decreaseQuantity(item) {
      if (item.quantity > 1) {
        item.quantity--;
      } else {
        this.itemToRemove = item; 
        this.showModal = true; 
      }
      this.updateLocalStorage();
    },
    confirmRemoval() {
      this.cart = this.cart.filter(cartItem => cartItem.id !== this.itemToRemove.id); // Remover o item
      this.updateLocalStorage();
      this.closeModal();
    },
    closeModal() {
      this.showModal = false;
      this.itemToRemove = null; 
    },
    clearCart() {
      this.cart = [];
      localStorage.removeItem('cart');
    },
    goBack() {
      this.$router.push('/');
    },
    updateLocalStorage() {
      localStorage.setItem('cart', JSON.stringify(this.cart));
    },
  },
};
</script>

<style scoped>
h1 {
  color: white;
}
body {
  background-color:#14151D;
}
.cart-item {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  padding: 15px;
  background-color: #23242C;
  color: white;
  border-radius: 10px;
}
.quantity-control {
  display: flex;
  align-items: center;
}
.btn-quantity {
  background-color: transparent; 
  color: white; 
  border: 2px solid white; 
  border-radius: 50%; 
  width: 35px; 
  height: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.quantity-control button {
  margin: 0 10px;
}
.clear-cart-button {
  background-color: #d9534f;
  color: white;
  padding: 10px;
  border: none;
  border-radius: 5px;
}
.cart-total {
  text-align: center;
  margin-top: 20px;
  color: white;
}

.cart-total h2 {
  margin-bottom: 20px; 
}

.buttons {
  display: flex;
  justify-content: center;
  gap: 10px; 
}
.modal {
  display: block !important;
}
</style>
