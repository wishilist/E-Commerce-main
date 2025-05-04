<template>
  <CustomerNav />
  <div class="checkout-container">
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
        Back to Products
      </router-link>
      <h1 class="page-title">Checkout</h1>
      <p class="page-subtitle">Complete your purchase</p>
    </div>

    <div class="checkout-content">
      <div class="checkout-form-section">
        <div class="order-summary">
          <h3 class="summary-title">Order Summary</h3>
          <div class="summary-items">
            <div
              v-for="(item, index) in cart"
              :key="index"
              class="summary-item"
            >
              <div class="item-info">
                <span class="item-name">{{ item.name }}</span>
                <span class="item-quantity">× {{ item.quantity }}</span>
              </div>
              <span class="item-price"
                >${{ (item.price * item.quantity).toFixed(2) }}</span
              >
            </div>
          </div>
          <div class="summary-total">
            <span>Total</span>
            <span class="total-amount">${{ total }}</span>
          </div>
        </div>

        <form @submit.prevent="submitOrder" class="checkout-form">
          <h3 class="form-title">Shipping Information</h3>
          <div class="form-group">
            <label for="address" class="form-label">Shipping Address</label>
            <textarea
              v-model="address"
              id="address"
              class="form-input"
              rows="3"
              placeholder="Enter your full shipping address"
              required
            ></textarea>
          </div>
          <button type="submit" class="submit-button">
            Place Order
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              fill="currentColor"
              class="submit-icon"
            >
              <path
                fill-rule="evenodd"
                d="M3 10a.75.75 0 01.75-.75h10.638L10.23 5.29a.75.75 0 111.04-1.08l5.5 5.25a.75.75 0 010 1.08l-5.5 5.25a.75.75 0 11-1.04-1.08l4.158-3.96H3.75A.75.75 0 013 10z"
                clip-rule="evenodd"
              />
            </svg>
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "../../axios";
import CustomerNav from "./CustomerNav.vue";

export default {
  components: {
    CustomerNav,
  },
  data() {
    return {
      cart: [],
      address: "",
      customerId: null,
    };
  },
  computed: {
    total() {
      return this.cart
        .reduce((sum, item) => sum + parseFloat(item.price) * item.quantity, 0)
        .toFixed(2);
    },
  },
  methods: {
    submitOrder() {
      if (this.cart.length === 0) {
        alert("Your cart is empty. Please add items before placing an order.");
        return;
      }

      const orderData = {
        customer: this.customerId,
        write_items: this.cart.map((item) => ({
          product: item.id,
          quantity: item.quantity,
        })),
      };

      axios
        .post("orders/", orderData)
        .then(() => {
          alert("Order placed successfully!");
          localStorage.removeItem("cart");
          this.$router.push("/customer/products");
        })
        .catch((err) => {
          console.error(err);
          alert("Something went wrong");
        });
    },
    getUserInfo() {
      axios
        .get("/users/")
        .then((res) => {
          this.customerId = res.data.customerId;
        })
        .catch(() => {
          alert("Could not get user info");
        });
    },
  },
  mounted() {
    this.cart = JSON.parse(localStorage.getItem("cart") || "[]");
    this.getUserInfo();
  },
};
</script>

<style scoped>
.checkout-container {
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
  margin: 0 0 0.25rem 0;
}

.page-subtitle {
  font-size: 1.125rem;
  color: #718096;
  margin: 0;
}

.checkout-content {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
}

.checkout-form-section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
}

.order-summary {
  background-color: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #edf2f7;
  height: fit-content;
}

.summary-title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0 0 1.5rem 0;
  padding-bottom: 1rem;
  border-bottom: 1px solid #e2e8f0;
}

.summary-items {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.summary-item {
  display: flex;
  justify-content: space-between;
}

.item-info {
  display: flex;
  gap: 0.5rem;
}

.item-name {
  font-weight: 500;
  color: #4a5568;
}

.item-quantity {
  color: #718096;
  font-size: 0.875rem;
}

.item-price {
  font-weight: 600;
  color: #2d3748;
}

.summary-total {
  display: flex;
  justify-content: space-between;
  font-weight: 700;
  color: #2d3748;
  font-size: 1.125rem;
  padding-top: 1rem;
  border-top: 1px solid #e2e8f0;
}

.total-amount {
  color: #4c51bf;
}

.checkout-form {
  background-color: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #edf2f7;
}

.form-title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0 0 1.5rem 0;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 500;
  color: #4a5568;
  font-size: 0.9375rem;
}

.form-input {
  width: 100%;
  padding: 0.85rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.2s ease;
  background-color: white;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
}

.form-input:focus {
  outline: none;
  border-color: #4c51bf;
  box-shadow: 0 0 0 3px rgba(76, 81, 191, 0.1);
}

textarea.form-input {
  min-height: 120px;
  resize: vertical;
}

.submit-button {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  width: 100%;
  padding: 1rem;
  background-color: #4c51bf;
  color: white;
  font-weight: 600;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.submit-button:hover {
  background-color: #434190;
}

.submit-icon {
  width: 1.25rem;
  height: 1.25rem;
}

@media (max-width: 768px) {
  .checkout-container {
    padding: 1.5rem;
  }

  .checkout-form-section {
    grid-template-columns: 1fr;
  }

  .page-title {
    font-size: 1.5rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }
}
</style>
