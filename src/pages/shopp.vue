<template>
    <q-layout view="hHh lpR fFf">
        <q-page-container>
            <q-header elevated>
                <q-toolbar>
                <q-toolbar-title>
                    <img class="small-image" src="~src/images/ilysmnanami.png" alt="Nanami's Shop" />
                    </q-toolbar-title>
                    <q-space />
                <q-btn @click="showCart" class="custom-badge">
                    <img class="badge-image" src="~src/images/cart.png" alt="Badge Image" />
                        {{ cartQty }}
                </q-btn>
                </q-toolbar>
            </q-header>

    <q-page>
        <!-- Search banner -->
        <section id="search-banner">
            <div class="search-banner-text">
                <h1>Get Our Fashion, Flaunt Your Passion at Nanami's Shop!</h1>
                    <q-form @submit="searchProducts">
                        <q-input
                    v-model="searchInput"
                    placeholder="Search your favorite item"
                    dense
                    outlined
                    :style="{ 'border-color': 'white', 'background-color': '#ffffff' }"/>
        
            </q-form>
            </div>
        </section>
        <!-- Search banner end -->



        <!-- Products -->
        <q-page-section class="products">
            <q-card v-for="product in filteredProducts" :key="product.id" class="product">
                <q-img :src="product.image" :alt="product.title" />
                <q-card-section>
                    <q-card-title>{{ product.title }}</q-card-title>
                    <q-card-subtitle>{{ product.price | formatCurrency }}</q-card-subtitle>
                    <q-btn :disable="isInCart(product.id)" @click="addToCart(product.id)" label="..."/>
            </q-card-section>
        </q-card>
        </q-page-section>
        <!-- Products end -->
    </q-page>



<!-- Cart -->
<q-dialog v-model="cartVisible">
    <q-card class="cart">
        <q-card-section class="cart-header">
            <h2>Your Cart</h2>
                <span class="cart-close" @click="closeCart">×</span>
    </q-card-section>
    <q-card-section class="cart-body">
    <q-list>
        <q-item v-if="cart.length === 0" class="cart-empty">
            <q-item-section>
            <q-item-label>Your cart is empty</q-item-label>
            </q-item-section>
        </q-item>
        <q-item v-else v-for="item in cart" :key="item.id" class="cart-item">
            <q-item-section>
                <q-img :src="item.image" :alt="item.title" class="cart-item-img" />
            </q-item-section>
                <q-item-section class="cart-item-detail">
                <q-item-label>{{ item.title }}</q-item-label>
                <q-item-label>{{ item.price | formatCurrency }}</q-item-label>
                <q-item-label>Quantity: {{ item.qty }}</q-item-label>
                </q-item-section>
            <q-item-section side class="cart-item-amount">
                <q-btn @click="increaseQty(item.id)" label ="‎ +" />
                <span class="qty">{{ item.qty }}</span>
                <q-btn @click="decreaseQty(item.id)" label ="—" />
        </q-item-section>
        </q-item>
        </q-list>
    </q-card-section>
    <q-card-actions class="cart-footer" align="right">
        <div>
        <strong>Total:</strong> {{ total | formatCurrency }}
        </div>
        <div>
        <q-btn class="cart-clear" label="Clear Cart" @click="clearCart" />
        <q-btn class="checkout" @click="checkout" label="Checkout" />
        </div>
    </q-card-actions>
    </q-card>
</q-dialog>
<!-- Cart end -->




        <!-- Footer -->
    
        <q-footer elevated>
            <q-toolbar class="glossy text-center">
                <q-toolbar-title class="text-subtitle2">©2023 Gail Buenaventura. All rights reserved; Reproduction or Transmission in any form without prior written permission is prohibited.</q-toolbar-title>
            </q-toolbar>
        </q-footer>
        <!-- Footer end -->


    </q-page-container>
    </q-layout>
</template>



<script>
import 'quasar/dist/quasar.css';
import '@quasar/extras/bootstrap-icons/bootstrap-icons.css';

export default {
  data() {
    return {
      products: [],
      cart: [],
      cartVisible: false,
      searchInput: '',
    };
  },
  computed: {
    filteredProducts() {
      const filterValue = this.searchInput.toUpperCase();
      return this.products.filter((product) =>
        product.title.toUpperCase().includes(filterValue)
      );
    },
    
    cartQty() {
      return this.cart.reduce((sum, item) => sum + item.qty, 0);
    },

    total() {
      return this.cart.reduce((sum, item) => sum + item.qty * item.price, 0);
    },

  },
  methods: {
    addToCart(id) {
      const inCart = this.cart.find((x) => x.id === id);
      if (inCart) {
        inCart.qty++;
        
        return;
      }

      const product = this.products.find((x) => x.id === id);
      this.cart.push({ id, qty: 1, ...product });
     
    },
    isInCart(id) {
      return this.cart.some((x) => x.id === id);
    },
    showCart() {
      this.cartVisible = true;
    },
    closeCart() {
      this.cartVisible = false;
    },
    clearCart() {
      this.cart = [];
      this.closeCart();
    },
    increaseQty(id) {
      const item = this.cart.find((x) => x.id === id);
      if (item) {
        item.qty++;
      }
    },
    decreaseQty(id) {
      const item = this.cart.find((x) => x.id === id);
      if (item) {
        if (item.qty > 1) {
          item.qty--;
        } else {
          this.removeFromCart(id);
        }
      }
    },
    removeFromCart(id) {
      this.cart = this.cart.filter((x) => x.id !== id);
      if (this.cart.length === 0) {
        this.closeCart();
      }
    },
    
    openSocialMedia(link) {
      window.open(link, '_blank');
    },
    
   
    async fetchProducts() {
      try {
        const response = await fetch('https://fakestoreapi.com/products');
        if (!response.ok) {
          throw new Error('Failed to fetch products');
        }
        this.products = await response.json();
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    },
  },
  filters: {
    formatCurrency(value) {
      return `$${value.toFixed(0)}`;
    },
  },
  mounted() {
   
    this.fetchProducts();
  },
};
</script>



<style>
@import url("https://fonts.googleapis.com/css2?family=Jost:wght@400;500;600&display=swap");

:root {
    --color1: #1638C5;
    --color2: #1432a9;
    --color3: #214aee;
    --color4: #5d70ba;
    --color5: #153cd6;
    --color6: #4a646c;
    --color7: #333;
    --color8: #fff;
    --color9: #1638C5;
    --transition: all 0.3s ease-in-out;
}

* {
    -family: "Jost", sans-serif;
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    

}

body {
    display: grid;
    grid-template-rows: auto 1fr;
    gap: 30px;
    min-height: 100vh;
    position: -webkit-sticky;
	position:sticky;
}

/* prevent body scroll when cart is open */
body:has(.show) {
    overflow: hidden;
}

button {
    cursor: pointer;
    border: none;
    border-radius: 3px;
    padding: 3px 10px;
    transition: var(--transition);
}

img {
    width: 100%;
    height: auto;
    display: block;
}

nav {
    background: linear-gradient(90deg, rgba(29,30,68,1) 0%, rgba(29,33,253,1) 50%, rgba(69,196,252,1) 97%);
    color: var(--color8);
    padding-block: 20px;
    position: -webkit-sticky;
	position:sticky;
}

nav > .container {
    display: flex;
    align-items: center;
    justify-content: space-between;

}



.logo img {
    height: 9rem;
}

.cart-btn {
    padding: 3px 8px;
    background: transparent;
    color: inherit;
    position: relative;
}


.cart-btn .bi {
    font-size: 1.9rem;
}

.cart-btn:hover {
    background: var(--color7);
}

.cart-qty {
    position: absolute;
    top: 0;
    right: 0;
    transform: translate(50%, -50%);
    background: var(--color7);
    padding: 0 5px;
    border-radius: 3px;
    display: none;
}

.cart-qty.visible {
    display: block;
}

.container {
    width: 1200px;
    width: 90%;
    margin: auto;

}




/*search bannerrr*/
#search-banner{
    width: 1300px;
    height: 400px;
    border-radius: 30px;
    background-color: #ecf7ee;
    margin: 10px auto;
    position: relative;
    overflow: hidden;
    background-image: url(../images/sheeshh.jpg);
    background-repeat: no-repeat;
    background-size: 1300px;

    padding: 20px;
    display: flex;
    align-items: center;
}



.menu{
    display: flex;
    list-style: none;
}
.menu li a{
    padding: 3px 10px;
    margin: 0px 15px;
    color: white;
    font-weight: 600;
    letter-spacing: 0.7px;
    transition: all ease 0.3s;
    font-size: 1.23rem;
    text-decoration: none;
    list-style: none;

}




.search-banner-text{
    display: flex;
    flex-direction: column;
    width: 500px;
    margin-left: 70px;
    position: relative;
}
.search-banner-text h1{
    font-size: 3.4rem;
    line-height: 57px;
    color: #ffffff;
    animation: glow 1s ease-in-out infinite alternate;

}   

@keyframes glow {
    from{
    text-shadow: 0 0 5px #fff, 0 0 5px blue, 0 0 5px blue, 0 0 5px blue,0 0 5px blue;
    }
    to {
    text-shadow: 0 0 5px yellow, 0 0 5px yellow;
    }
}


.search-banner{
    background-color: #ffffff;
    height: 50px;
    display: flex;
    align-items: center;
    margin-top: 25px;
    padding: 0px 5px 0px 20px;
    border-radius: 30px;
}



.search-banner i{
    font-size: 1.3rem;
    color: #3b3b3b;
    margin: 0px 10px;
}
.search-box .search-input{
    height: 40px;
    border: none;
    width: 100%;
    padding: 0px 10px;
    outline: none;
    color: white;
}


.search-box .search-btn{
    width:220px;
    height: 40px;
    border-radius: 30px;
    background-color: rgba(53,53,212,1);
    border: none;
    color: #ffffff;
    outline: none;
    cursor: pointer;
    transition: all ease 0.3s;
}

.search-box .search-btn:hover{
    background-color: black;
    transition: all ease 0.3s;
}




.products {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap:50px;
}

.product {

    border: 1px solid black;
    border-radius: 20px;
    background-color: #ffffff;
    padding: 20px;
    display: grid;
    flex-direction: column;
    position: relative;
    box-shadow: 10px 8px 0px #1638C5;
    width: 100%;
    gap: 20px;
    


    max-width: 250px;
    margin: 0 auto;
    height: 400px;

}

.product img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 10px;

}

.product:hover img {
    opacity: 0.75;
}

.product h3 {
    margin-top: 10px;
    color: black;
    font-size: 1rem;
}

.product h5 {
    margin-top: 5px;
    color: var(--color6);
}

.product button {
    position: absolute;
    top: 10px;
    right: 10px;
    background: var(--color9);
    color: var(--color8);
    padding: 5px 10px;
    font-size: 1rem;
    display: flex;
    align-items: center;
    gap: 5px;
    opacity: 0;
}

.product:hover button {
    opacity: 1;
}

.product button::before {
    font-family: "bootstrap-icons";
    font-size: 1.5rem;
    content: "\F23F";
}

.product button:disabled::before {
    content: "\F23A";
}

.product button:hover {
    background: black;
}

.product button:disabled {
    background: var(--color3);
    filter: brightness(1.75);
}

/* cart */

.cart-overlay {
    position: fixed;
    inset: 0;
    opacity: 0.5;
    visibility: hidden;
    cursor: pointer;
    background: var(--color8);
    transition: var(--transition);
}

.cart-overlay.show {
    visibility: visible;
}

.cart {
  position: fixed;
  inset-block: 0;
  left: 27%;
  width: 100%;
  max-width: 420px;
  padding: 20px;
  display: grid;
  grid-template-rows: auto 1fr auto;
  transform: translateX(100%);
  transition: var(--transition);
  background: var(--color8);
}

.cart.show {
  transform: none;
}

.cart-header {
  position: relative;
  text-align: center;
  padding-block: 10px;
  border-bottom: 1px solid #ddd;
}

.cart-close {
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  font-size: 1.5rem;
  cursor: pointer;
}

.cart-body {
  display: grid;
  align-content: start;
  
  padding-block: 20px;
  overflow: auto;
}


.cart-body:has(.cart-empty) + .cart-footer {
  display: none;
}

.cart-empty {
  text-align: center;
  padding-block: 20px;
  font-size: 1.25rem;
  color: var(--color6);
}

.cart-item {
  display: flex;
  align-items: center;
  gap: 100px;

}

.cart-item img {
  width: 100px;
  height: 100px;
  object-fit: cover;
  
}

.cart-item-detail {
  display: flex;
  flex-direction: column;
  gap: 1px;
}




.cart-item-amount {
  margin-top: auto;
  display: flex;
  align-items: center;
  gap: 1px;
  font-size: 1.25rem;
}

.cart-item-amount .bi {
  cursor: pointer;
  opacity: 0.25;
}

.cart-item-amount .bi:hover {
  opacity: 1;
}

.cart-item-amount .qty {
  width: 2rem;
  text-align: center;
}

.cart-item-price {
  margin-left: auto;
}

.cart-footer {
  border-top: 1px solid #ddd;
  padding-block: 10px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
}

.cart-footer div {
  grid-column: 1 / -1;
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 1.25rem;
}

.cart-footer strong {
  font-weight: 500;
  color: black;
}

.cart-footer button {
  padding-block: 10px;
  text-transform: uppercase;
}

.cart-clear {
  background: var(--color7);
  color: var(--color8);
}

.cart-clear:hover {
  filter: brightness(1.75);
}

.checkout {
  background: var(--color2);
  color: var(--color8);
  text-decoration: none;
}

.checkout a{
  text-decoration: none;
  color: white;
}

.checkout:hover {
  background: grey;
}

.small-image {
  width: 200px;
  height: auto;
  margin-left: 2em;
}


.badge-image {
  width: 30px; 
  height: 30px;
  border-radius: 50%;
  margin-right: 2px; 
  margin-left: 2px; 
}

.cart-header h2{

font-size: 3em;
}








</style>

