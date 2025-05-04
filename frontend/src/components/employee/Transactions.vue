<template>
  <EmployeeNav />
  <div class="transactions-container">
    <div class="header-section">
      <h1 class="page-title">Transaction History</h1>
      <p class="page-subtitle">View and manage customer orders</p>
    </div>

    <div class="filter-section">
      <div class="date-filter">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
          class="filter-icon"
        >
          <path
            fill-rule="evenodd"
            d="M5.75 2a.75.75 0 01.75.75V4h7V2.75a.75.75 0 011.5 0V4h.25A2.75 2.75 0 0118 6.75v8.5A2.75 2.75 0 0115.25 18H4.75A2.75 2.75 0 012 15.25v-8.5A2.75 2.75 0 014.75 4H5V2.75A.75.75 0 015.75 2zm-1 5.5c-.69 0-1.25.56-1.25 1.25v6.5c0 .69.56 1.25 1.25 1.25h10.5c.69 0 1.25-.56 1.25-1.25v-6.5c0-.69-.56-1.25-1.25-1.25H4.75z"
            clip-rule="evenodd"
          />
        </svg>
        <input type="date" v-model="filterDate" class="date-input" />
      </div>
    </div>

    <div v-if="filteredOrders.length === 0" class="empty-state">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="currentColor"
        class="empty-icon"
      >
        <path
          fill-rule="evenodd"
          d="M2.25 13.5a8.25 8.25 0 018.25-8.25.75.75 0 01.75.75v6.75H18a.75.75 0 01.75.75 8.25 8.25 0 01-16.5 0z"
          clip-rule="evenodd"
        />
        <path
          fill-rule="evenodd"
          d="M12.75 3a.75.75 0 01.75-.75 8.25 8.25 0 018.25 8.25.75.75 0 01-.75.75h-7.5a.75.75 0 01-.75-.75V3z"
          clip-rule="evenodd"
        />
      </svg>
      <h3>No transactions found</h3>
      <p>Try adjusting your date filter or check back later</p>
    </div>

    <div v-else class="transactions-grid">
      <div
        v-for="order in filteredOrders"
        :key="order.id"
        class="transaction-card"
      >
        <div class="card-header">
          <div class="order-info">
            <h3 class="order-number">Order #{{ order.id }}</h3>
            <span class="order-date">{{ formatDate(order.created_at) }}</span>
          </div>
          <span class="order-total"
            >${{ getOrderTotal(order.items).toFixed(2) }}</span
          >
        </div>

        <button class="toggle-products" @click="toggleProducts(order.id)">
          {{ expandedOrders.includes(order.id) ? "Hide items" : "View items" }}
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
            fill="currentColor"
            class="toggle-icon"
          >
            <path
              fill-rule="evenodd"
              d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
              clip-rule="evenodd"
            />
          </svg>
        </button>

        <div v-if="expandedOrders.includes(order.id)" class="product-list">
          <div v-if="order.items && order.items.length" class="items-container">
            <div
              v-for="item in order.items"
              :key="item.product.id"
              class="product-item"
            >
              <span class="product-name">{{ item.product.name }}</span>
              <span class="product-quantity">× {{ item.quantity }}</span>
              <span class="product-price"
                >${{ (item.product.price * item.quantity).toFixed(2) }}</span
              >
            </div>
          </div>
          <div v-else class="loading-items">Loading items...</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "../../axios";
import EmployeeNav from "./EmployeeNav.vue";

export default {
  components: {
    EmployeeNav,
  },
  data() {
    return {
      orders: [],
      expandedOrders: [],
      filterDate: "",
    };
  },
  computed: {
    filteredOrders() {
      if (!this.filterDate) return this.orders;
      return this.orders.filter((order) => {
        const orderDate = new Date(order.created_at)
          .toISOString()
          .split("T")[0];
        return orderDate === this.filterDate;
      });
    },
  },
  methods: {
    async fetchOrders() {
      try {
        const res = await axios.get("/orders/");
        this.orders = res.data;
      } catch (err) {
        alert("Failed to load transactions");
      }
    },
    getOrderTotal(items) {
      if (!items) return 0;
      return items.reduce((total, item) => {
        return total + item.product.price * item.quantity;
      }, 0);
    },
    async toggleProducts(orderId) {
      const index = this.expandedOrders.indexOf(orderId);
      if (index !== -1) {
        this.expandedOrders.splice(index, 1);
      } else {
        const order = this.orders.find((o) => o.id === orderId);
        if (!order.items) {
          try {
            const res = await axios.get(`/orders/${orderId}/`);
            order.items = res.data.items;
          } catch (err) {
            alert("Failed to load products for order");
            return;
          }
        }
        this.expandedOrders.push(orderId);
      }
    },
    formatDate(dateString) {
      const options = { year: "numeric", month: "short", day: "numeric" };
      return new Date(dateString).toLocaleDateString(undefined, options);
    },
  },
  mounted() {
    this.fetchOrders();
  },
};
</script>

<style scoped>
.transactions-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  font-family: "Inter", -apple-system, BlinkMacSystemFont, sans-serif;
}

.header-section {
  margin-bottom: 2.5rem;
  text-align: center;
}

.page-title {
  font-size: 2rem;
  font-weight: 700;
  color: #2d3748;
  margin-bottom: 0.5rem;
}

.page-subtitle {
  font-size: 1.125rem;
  color: #718096;
  margin: 0;
}

.filter-section {
  margin-bottom: 2rem;
  display: flex;
  justify-content: flex-end;
}

.date-filter {
  display: flex;
  align-items: center;
  background-color: white;
  border-radius: 8px;
  padding: 0.5rem 1rem;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #e2e8f0;
}

.filter-icon {
  width: 1.25rem;
  height: 1.25rem;
  color: #718096;
  margin-right: 0.75rem;
}

.date-input {
  border: none;
  outline: none;
  font-size: 1rem;
  color: #4a5568;
  background: transparent;
}

.date-input::-webkit-calendar-picker-indicator {
  cursor: pointer;
  opacity: 0.6;
  transition: opacity 0.2s;
}

.date-input::-webkit-calendar-picker-indicator:hover {
  opacity: 1;
}

.empty-state {
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

.empty-state h3 {
  font-size: 1.25rem;
  color: #2d3748;
  margin-bottom: 0.5rem;
}

.empty-state p {
  color: #718096;
  font-size: 1rem;
  margin-bottom: 1.5rem;
}

.transactions-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
  gap: 1.5rem;
}

.transaction-card {
  background-color: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  border: 1px solid #edf2f7;
  transition: all 0.3s ease;
}

.transaction-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.25rem;
}

.order-info {
  display: flex;
  flex-direction: column;
}

.order-number {
  font-size: 1.125rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0 0 0.25rem 0;
}

.order-date {
  font-size: 0.875rem;
  color: #718096;
}

.order-total {
  font-weight: 700;
  color: #4c51bf;
  font-size: 1.125rem;
}

.toggle-products {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  background: none;
  border: none;
  color: #4c51bf;
  font-weight: 500;
  cursor: pointer;
  padding: 0.5rem 0;
  transition: color 0.2s;
}

.toggle-products:hover {
  color: #434190;
}

.toggle-icon {
  width: 1rem;
  height: 1rem;
  transition: transform 0.2s;
}

.toggle-products[aria-expanded="true"] .toggle-icon {
  transform: rotate(180deg);
}

.product-list {
  margin-top: 1rem;
  border-top: 1px solid #edf2f7;
  padding-top: 1rem;
}

.items-container {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.product-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.75rem;
  background-color: #f8fafc;
  border-radius: 8px;
}

.product-name {
  flex: 1;
  font-weight: 500;
  color: #4a5568;
}

.product-quantity {
  margin: 0 1rem;
  color: #718096;
  font-size: 0.875rem;
}

.product-price {
  font-weight: 600;
  color: #2d3748;
}

.loading-items {
  color: #718096;
  text-align: center;
  padding: 1rem;
}

@media (max-width: 768px) {
  .transactions-container {
    padding: 1.5rem;
  }

  .transactions-grid {
    grid-template-columns: 1fr;
  }

  .page-title {
    font-size: 1.5rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }

  .filter-section {
    justify-content: center;
  }
}
</style>
