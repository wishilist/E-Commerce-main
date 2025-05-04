<template>
  <CustomerNav />
  <div class="cart-container">
    <div class="header-section">
      <router-link to="/customer/products" class="back-button">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
          class="back-icon"
        >
          <path
            fill-rule="evenodd"
            d="M17 10a.75.75 0 01-.75.75H5.612l4.158 3.96a.75.75 0 11-1.04 1.08l-5.5-5.25a.75.75 0 010-1.08l5.5-5.25a.75.75 0 111.04 1.08L5.612 9.25H16.25A.75.75 0 0117 10z"
            clip-rule="evenodd"
          />
        </svg>
        Continue Shopping
      </router-link>
      <h1 class="page-title">Your Shopping Cart</h1>
    </div>

    <div v-if="cart.length" class="cart-content">
      <div class="cart-items">
        <div class="cart-item" v-for="(item, index) in cart" :key="index">
          <div class="item-image-container">
            <img
              :src="
                item.image ||
                'https://via.placeholder.com/100x100.png?text=No+Image'
              "
              class="item-image"
              alt="Product Image"
            />
          </div>
          <div class="item-details">
            <h3 class="item-name">{{ item.name }}</h3>
            <p class="item-description">
              {{ item.description || "No description available" }}
            </p>
            <div class="item-meta">
              <span class="item-price">${{ item.price }}</span>
              <div class="quantity-controls">
                <button
                  class="quantity-btn"
                  @click="decreaseQuantity(index)"
                  :disabled="item.quantity <= 1"
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                    fill="currentColor"
                    class="quantity-icon"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M4 10a.75.75 0 01.75-.75h10.5a.75.75 0 010 1.5H4.75A.75.75 0 014 10z"
                      clip-rule="evenodd"
                    />
                  </svg>
                </button>
                <span class="quantity">{{ item.quantity }}</span>
                <button class="quantity-btn" @click="increaseQuantity(index)">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                    fill="currentColor"
                    class="quantity-icon"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M10 5a.75.75 0 01.75.75v4.5h4.5a.75.75 0 010 1.5h-4.5v4.5a.75.75 0 01-1.5 0v-4.5h-4.5a.75.75 0 010-1.5h4.5v-4.5A.75.75 0 0110 5z"
                      clip-rule="evenodd"
                    />
                  </svg>
                </button>
              </div>
            </div>
          </div>
          <div class="item-actions">
            <span class="item-subtotal"
              >${{ (item.price * item.quantity).toFixed(2) }}</span
            >
            <button class="remove-button" @click="removeItem(index)">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="currentColor"
                class="remove-icon"
              >
                <path
                  fill-rule="evenodd"
                  d="M8.75 1A2.75 2.75 0 006 3.75v.443c-.795.077-1.584.176-2.365.298a.75.75 0 10.23 1.482l.149-.022.841 10.518A2.75 2.75 0 007.596 19h4.807a2.75 2.75 0 002.742-2.53l.841-10.52.149.023a.75.75 0 00.23-1.482A41.03 41.03 0 0014 4.193V3.75A2.75 2.75 0 0011.25 1h-2.5zM10 4c.84 0 1.673.025 2.5.075V3.75c0-.69-.56-1.25-1.25-1.25h-2.5c-.69 0-1.25.56-1.25 1.25v.325C8.327 4.025 9.16 4 10 4zM8.58 7.72a.75.75 0 00-1.5.06l.3 7.5a.75.75 0 101.5-.06l-.3-7.5zm4.34.06a.75.75 0 10-1.5-.06l-.3 7.5a.75.75 0 101.5.06l.3-7.5z"
                  clip-rule="evenodd"
                />
              </svg>
            </button>
          </div>
        </div>
      </div>

      <div class="cart-summary">
        <div class="summary-card">
          <h3 class="summary-title">Order Summary</h3>
          <div class="summary-row">
            <span>Subtotal</span>
            <span>${{ subtotal }}</span>
          </div>
          <div class="summary-row">
            <span>Estimated Shipping</span>
            <span>Free</span>
          </div>
          <div class="summary-row total">
            <span>Total</span>
            <span>${{ total }}</span>
          </div>
          <router-link to="/customer/checkout" class="checkout-button">
            Proceed to Checkout
          </router-link>
        </div>
      </div>
    </div>

    <div v-else class="empty-cart">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="currentColor"
        class="empty-icon"
      >
        <path
          d="M2.25 2.25a.75.75 0 000 1.5h1.386c.17 0 .318.114.362.278l2.558 9.592a3.752 3.752 0 00-2.806 3.63c0 .414.336.75.75.75h15.75a.75.75 0 000-1.5H5.378A2.25 2.25 0 017.5 15h11.218a.75.75 0 00.674-.421 60.358 60.358 0 002.96-7.228.75.75 0 00-.525-.965A60.864 60.864 0 005.68 4.509l-.232-.867A1.875 1.875 0 003.636 2.25H2.25zM3.75 20.25a1.5 1.5 0 113 0 1.5 1.5 0 01-3 0zM16.5 20.25a1.5 1.5 0 113 0 1.5 1.5 0 01-3 0z"
        />
      </svg>
      <h3>Your cart is empty</h3>
      <p>Browse our products and add items to get started</p>
      <router-link to="/customer/products" class="shop-button">
        Shop Products
      </router-link>
    </div>
  </div>
</template>

<script>
import CustomerNav from "./CustomerNav.vue";

export default {
  components: {
    CustomerNav,
  },
  data() {
    return {
      cart: [],
    };
  },
  computed: {
    subtotal() {
      return this.cart
        .reduce((sum, item) => sum + item.price * item.quantity, 0)
        .toFixed(2);
    },
    total() {
      return this.subtotal; // Add tax/shipping logic if needed
    },
  },
  methods: {
    loadCart() {
      const rawCart = JSON.parse(localStorage.getItem("cart") || "[]");
      this.cart = rawCart.map((item) => ({
        ...item,
        quantity: item.quantity || 1,
      }));
    },
    removeItem(index) {
      this.cart.splice(index, 1);
      this.saveCart();
    },
    increaseQuantity(index) {
      this.cart[index].quantity += 1;
      this.saveCart();
    },
    decreaseQuantity(index) {
      if (this.cart[index].quantity > 1) {
        this.cart[index].quantity -= 1;
        this.saveCart();
      }
    },
    saveCart() {
      localStorage.setItem("cart", JSON.stringify(this.cart));
    },
  },
  mounted() {
    this.loadCart();
  },
};
</script>

<style scoped>
.cart-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  font-family: "Inter", -apple-system, BlinkMacSystemFont, sans-serif;
}

.header-section {
  margin-bottom: 2rem;
}

.back-button {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: #4c51bf;
  font-weight: 500;
  text-decoration: none;
  margin-bottom: 1rem;
  transition: color 0.2s;
}

.back-button:hover {
  color: #434190;
}

.back-icon {
  width: 1.25rem;
  height: 1.25rem;
}

.page-title {
  font-size: 2rem;
  font-weight: 700;
  color: #2d3748;
  margin: 0;
}

.cart-content {
  display: grid;
  grid-template-columns: 1fr 350px;
  gap: 2rem;
}

.cart-items {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.cart-item {
  display: flex;
  gap: 1.5rem;
  padding: 1.5rem;
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #edf2f7;
}

.item-image-container {
  width: 100px;
  height: 100px;
  flex-shrink: 0;
  border-radius: 8px;
  overflow: hidden;
  background-color: #f8fafc;
}

.item-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.item-details {
  flex: 1;
}

.item-name {
  font-size: 1.125rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0 0 0.5rem 0;
}

.item-description {
  color: #718096;
  font-size: 0.875rem;
  margin-bottom: 1rem;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.item-meta {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.item-price {
  font-weight: 700;
  color: #4c51bf;
}

.quantity-controls {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  background-color: #f8fafc;
  border-radius: 8px;
  padding: 0.25rem 0.5rem;
}

.quantity-btn {
  background: none;
  border: none;
  color: #4c51bf;
  cursor: pointer;
  padding: 0.25rem;
  border-radius: 4px;
  transition: background-color 0.2s;
}

.quantity-btn:hover {
  background-color: #e2e8f0;
}

.quantity-btn:disabled {
  color: #cbd5e0;
  cursor: not-allowed;
}

.quantity-btn:disabled:hover {
  background-color: transparent;
}

.quantity-icon {
  width: 1rem;
  height: 1rem;
}

.quantity {
  font-weight: 500;
  min-width: 1.5rem;
  text-align: center;
}

.item-actions {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: space-between;
}

.item-subtotal {
  font-weight: 700;
  color: #2d3748;
}

.remove-button {
  background: none;
  border: none;
  color: #e53e3e;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 4px;
  transition: background-color 0.2s;
}

.remove-button:hover {
  background-color: #fff5f5;
}

.remove-icon {
  width: 1.25rem;
  height: 1.25rem;
}

.cart-summary {
  position: sticky;
  top: 1rem;
  height: fit-content;
}

.summary-card {
  background-color: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #edf2f7;
}

.summary-title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0 0 1.5rem 0;
  padding-bottom: 1rem;
  border-bottom: 1px solid #e2e8f0;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1rem;
  color: #4a5568;
}

.summary-row.total {
  font-weight: 700;
  color: #2d3748;
  font-size: 1.125rem;
  margin-top: 1.5rem;
  padding-top: 1rem;
  border-top: 1px solid #e2e8f0;
}

.checkout-button {
  display: block;
  width: 100%;
  text-align: center;
  padding: 1rem;
  background-color: #4c51bf;
  color: white;
  font-weight: 600;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s;
  text-decoration: none;
  margin-top: 1.5rem;
}

.checkout-button:hover {
  background-color: #434190;
}

.empty-cart {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 4rem 2rem;
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #edf2f7;
  text-align: center;
}

.empty-icon {
  width: 3.5rem;
  height: 3.5rem;
  color: #c3dafe;
  margin-bottom: 1.5rem;
}

.empty-cart h3 {
  font-size: 1.25rem;
  color: #2d3748;
  margin-bottom: 0.5rem;
}

.empty-cart p {
  color: #718096;
  font-size: 1rem;
  margin-bottom: 1.5rem;
}

.shop-button {
  padding: 0.75rem 1.5rem;
  background-color: #4c51bf;
  color: white;
  font-weight: 600;
  border-radius: 8px;
  text-decoration: none;
  transition: background-color 0.2s;
}

.shop-button:hover {
  background-color: #434190;
}

@media (max-width: 768px) {
  .cart-container {
    padding: 1.5rem;
  }

  .cart-content {
    grid-template-columns: 1fr;
  }

  .cart-summary {
    position: static;
    margin-top: 2rem;
  }

  .cart-item {
    flex-direction: column;
    gap: 1rem;
  }

  .item-image-container {
    width: 100%;
    height: 150px;
  }

  .item-actions {
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    margin-top: 1rem;
  }

  .page-title {
    font-size: 1.5rem;
  }
}
</style>
