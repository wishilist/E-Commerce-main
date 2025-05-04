<template>
  <CustomerNav />
  <div class="product-list-container">
    <div class="header-section">
      <h1 class="page-title">Our Products</h1>
      <p class="page-subtitle">Quality items for your shopping needs</p>
    </div>

    <div class="search-container">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
        fill="currentColor"
        class="search-icon"
      >
        <path
          fill-rule="evenodd"
          d="M9 3.5a5.5 5.5 0 100 11 5.5 5.5 0 000-11zM2 9a7 7 0 1112.452 4.391l3.328 3.329a.75.75 0 11-1.06 1.06l-3.329-3.328A7 7 0 012 9z"
          clip-rule="evenodd"
        />
      </svg>
      <input
        type="text"
        v-model="searchQuery"
        class="search-input"
        placeholder="Search products..."
      />
    </div>

    <div v-if="filteredProducts.length" class="product-grid">
      <div
        class="product-card"
        v-for="product in filteredProducts"
        :key="product.id"
      >
        <div class="product-image-container">
          <img
            :src="
              product.image ||
              'https://via.placeholder.com/300x200.png?text=No+Image'
            "
            class="product-image"
            alt="Product Image"
          />
        </div>
        <div class="product-details">
          <h3 class="product-name">{{ product.name }}</h3>
          <p class="product-description">{{ product.description }}</p>
          <div class="product-meta">
            <span class="product-price">${{ product.price }}</span>
            <span class="product-stock">{{ product.stock }} in stock</span>
          </div>
          <button class="add-to-cart-button" @click="addToCart(product)">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              fill="currentColor"
              class="cart-icon"
            >
              <path
                d="M1 1.75A.75.75 0 011.75 1h1.628a1.75 1.75 0 011.734 1.51L5.18 3a65.25 65.25 0 0113.36 1.412.75.75 0 01.58.875 48.645 48.645 0 01-1.618 6.2.75.75 0 01-.712.513H6a2.503 2.503 0 00-2.292 1.5H17.25a.75.75 0 010 1.5H2.76a.75.75 0 01-.748-.807 4.002 4.002 0 01.716-2.43L.124 3.608A.75.75 0 011 1.75zM5 8a.75.75 0 01.75-.75h2.5a.75.75 0 010 1.5h-2.5A.75.75 0 015 8zm3.25 0a.75.75 0 01.75-.75h2.5a.75.75 0 010 1.5h-2.5a.75.75 0 01-.75-.75zM5.75 11.5a.75.75 0 000 1.5h4.5a.75.75 0 000-1.5h-4.5z"
              />
            </svg>
            Add to Cart
          </button>
        </div>
      </div>
    </div>

    <div v-else class="empty-state">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="currentColor"
        class="empty-icon"
      >
        <path
          fill-rule="evenodd"
          d="M9.401 3.003c1.155-2 4.043-2 5.197 0l7.355 12.748c1.154 2-.29 4.5-2.599 4.5H4.645c-2.309 0-3.752-2.5-2.598-4.5L9.4 3.003zM12 8.25a.75.75 0 01.75.75v3.75a.75.75 0 01-1.5 0V9a.75.75 0 01.75-.75zm0 8.25a.75.75 0 100-1.5.75.75 0 000 1.5z"
          clip-rule="evenodd"
        />
      </svg>
      <h3>No products found</h3>
      <p>Try adjusting your search or check back later</p>
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
      products: [],
      searchQuery: "",
    };
  },
  computed: {
    filteredProducts() {
      if (!this.searchQuery) {
        return this.products; // If no search, return all products
      }
      return this.products.filter((product) => {
        return product.name
          .toLowerCase()
          .includes(this.searchQuery.toLowerCase()); // Filter products based on search query
      });
    },
  },
  methods: {
    fetchProducts() {
      axios.get("products/").then((res) => {
        this.products = res.data;
      });
    },
    logout() {
      localStorage.clear();
      this.$router.push("/login");
    },
    addToCart(product) {
      let quantity = parseInt(prompt("Enter quantity:"));
      if (isNaN(quantity) || quantity <= 0) {
        alert("Please enter a valid quantity.");
        return;
      }

      const cart = JSON.parse(localStorage.getItem("cart") || "[]");
      const productWithQuantity = { ...product, quantity };
      cart.push(productWithQuantity);
      localStorage.setItem("cart", JSON.stringify(cart));
      alert(`Added ${quantity} of ${product.name} to cart.`);
    },
  },
  mounted() {
    this.fetchProducts();
  },
};
</script>

<style scoped>
.product-list-container {
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

.search-container {
  position: relative;
  margin-bottom: 2rem;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.search-icon {
  position: absolute;
  left: 1rem;
  top: 50%;
  transform: translateY(-50%);
  width: 1.25rem;
  height: 1.25rem;
  color: #a0aec0;
}

.search-input {
  width: 100%;
  padding: 0.85rem 1rem 0.85rem 3rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 1rem;
  background-color: white;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  transition: all 0.2s ease;
}

.search-input:focus {
  outline: none;
  border-color: #4c51bf;
  box-shadow: 0 0 0 3px rgba(76, 81, 191, 0.1);
}

.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 1.5rem;
}

.product-card {
  background-color: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
  border: 1px solid #edf2f7;
}

.product-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}

.product-image-container {
  height: 200px;
  overflow: hidden;
  background-color: #f8fafc;
}

.product-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.4s ease;
}

.product-card:hover .product-image {
  transform: scale(1.03);
}

.product-details {
  padding: 1.5rem;
}

.product-name {
  font-size: 1.125rem;
  font-weight: 600;
  color: #2d3748;
  margin-bottom: 0.75rem;
}

.product-description {
  color: #718096;
  font-size: 0.9375rem;
  line-height: 1.5;
  margin-bottom: 1.25rem;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.product-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.product-price {
  font-weight: 700;
  color: #4c51bf;
  font-size: 1.125rem;
}

.product-stock {
  color: #718096;
  font-size: 0.875rem;
  font-weight: 500;
}

.add-to-cart-button {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  width: 100%;
  padding: 0.75rem;
  background-color: #4c51bf;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
}

.add-to-cart-button:hover {
  background-color: #434190;
}

.cart-icon {
  width: 1.25rem;
  height: 1.25rem;
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

@media (max-width: 768px) {
  .product-list-container {
    padding: 1.5rem;
  }

  .product-grid {
    grid-template-columns: 1fr;
  }

  .page-title {
    font-size: 1.5rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }

  .search-container {
    max-width: 100%;
  }
}
</style>
