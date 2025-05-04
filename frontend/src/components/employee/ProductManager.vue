<template>
  <EmployeeNav />
  <div class="product-manager-container">
    <div class="header-section">
      <h1 class="product-manager-title">Product Management</h1>
      <p class="subtitle">Manage your inventory with ease</p>
    </div>

    <div class="product-manager-actions">
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
          v-model="searchQuery"
          type="text"
          class="search-input"
          placeholder="Search products..."
        />
      </div>

      <button class="add-product-button" @click="openCreateForm">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
          class="button-icon"
        >
          <path
            d="M10.75 4.75a.75.75 0 00-1.5 0v4.5h-4.5a.75.75 0 000 1.5h4.5v4.5a.75.75 0 001.5 0v-4.5h4.5a.75.75 0 000-1.5h-4.5v-4.5z"
          />
        </svg>
        New Product
      </button>
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
          <div class="product-header">
            <h3 class="product-name">{{ product.name }}</h3>
            <span class="product-price">${{ product.price }}</span>
          </div>
          <p class="product-description">{{ product.description }}</p>
          <div class="product-footer">
            <span class="product-stock">{{ product.stock }} in stock</span>
            <div class="product-actions">
              <button class="edit-button" @click="openEditForm(product)">
                Edit
              </button>
              <button class="delete-button" @click="deleteProduct(product.id)">
                Delete
              </button>
            </div>
          </div>
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
      <p>Try adding a new product or adjusting your search</p>
      <button class="add-product-button" @click="openCreateForm">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
          class="button-icon"
        >
          <path
            d="M10.75 4.75a.75.75 0 00-1.5 0v4.5h-4.5a.75.75 0 000 1.5h4.5v4.5a.75.75 0 001.5 0v-4.5h4.5a.75.75 0 000-1.5h-4.5v-4.5z"
          />
        </svg>
        Add Product
      </button>
    </div>

    <div v-if="showModal" class="modal-overlay">
      <div class="modal-content">
        <form @submit.prevent="submitProduct" class="product-form">
          <div class="modal-header">
            <h3>{{ isEditing ? "Edit Product" : "Create New Product" }}</h3>
            <button type="button" class="close-button" @click="closeModal">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="currentColor"
              >
                <path
                  d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z"
                />
              </svg>
            </button>
          </div>

          <div class="modal-body">
            <div class="form-group">
              <label>Product Name</label>
              <input
                v-model="form.name"
                class="form-input"
                placeholder="Enter product name"
                required
              />
            </div>

            <div class="form-row">
              <div class="form-group">
                <label>Price ($)</label>
                <input
                  type="number"
                  v-model="form.price"
                  class="form-input"
                  placeholder="e.g. 29.99"
                  required
                />
              </div>

              <div class="form-group">
                <label>Stock Quantity</label>
                <input
                  type="number"
                  v-model="form.stock"
                  class="form-input"
                  required
                />
              </div>
            </div>

            <div class="form-group">
              <label>Description</label>
              <textarea
                v-model="form.description"
                class="form-input"
                placeholder="Product details"
                rows="3"
                required
              ></textarea>
            </div>

            <div class="form-group">
              <label>Product Image</label>
              <div class="file-upload">
                <input type="file" @change="handleImage" id="product-image" />
                <label for="product-image" class="upload-label">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                    fill="currentColor"
                    class="upload-icon"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M5.5 17a4.5 4.5 0 01-1.44-8.765 4.5 4.5 0 018.302-3.046 3.5 3.5 0 014.504 4.272A4 4 0 0115 17H5.5zm3.75-2.75a.75.75 0 001.5 0V9.66l1.95 2.1a.75.75 0 101.1-1.02l-3.25-3.5a.75.75 0 00-1.1 0l-3.25 3.5a.75.75 0 101.1 1.02l1.95-2.1v4.59z"
                      clip-rule="evenodd"
                    />
                  </svg>
                  <span>{{
                    form.image ? form.image.name : "Choose an image..."
                  }}</span>
                </label>
              </div>
            </div>
          </div>

          <div class="modal-footer">
            <button type="button" class="cancel-button" @click="closeModal">
              Cancel
            </button>
            <button type="submit" class="submit-button">
              {{ isEditing ? "Update Product" : "Create Product" }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Base Styles */
.product-manager-container {
  padding: 2rem 4rem;
  max-width: 1400px;
  margin: 0 auto;
  font-family: "Segoe UI", "Roboto", "Oxygen", "Ubuntu", sans-serif;
}

.header-section {
  margin-bottom: 2.5rem;
  text-align: center;
}

.product-manager-title {
  font-size: 2.25rem;
  font-weight: 700;
  color: #2d3748;
  margin-bottom: 0.5rem;
}

.subtitle {
  font-size: 1.125rem;
  color: #718096;
  margin: 0;
}

/* Actions Bar */
.product-manager-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2.5rem;
  gap: 1.5rem;
}

.search-container {
  position: relative;
  flex-grow: 1;
  max-width: 500px;
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

.add-product-button {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.85rem 1.5rem;
  background-color: #4c51bf;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
}

.add-product-button:hover {
  background-color: #434190;
  transform: translateY(-1px);
}

.button-icon {
  width: 1.25rem;
  height: 1.25rem;
}

/* Product Grid */
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 1.75rem;
}

.product-card {
  background-color: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05),
    0 2px 4px -1px rgba(0, 0, 0, 0.02);
  transition: all 0.3s ease;
  border: 1px solid #edf2f7;
}

.product-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1),
    0 4px 6px -2px rgba(0, 0, 0, 0.05);
  border-color: #e2e8f0;
}

.product-image-container {
  height: 200px;
  overflow: hidden;
  background-color: #f7fafc;
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

.product-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1rem;
}

.product-name {
  font-size: 1.125rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0;
  flex: 1;
}

.product-price {
  font-weight: 700;
  color: #4c51bf;
  font-size: 1.125rem;
  margin-left: 1rem;
}

.product-description {
  color: #718096;
  font-size: 0.9375rem;
  line-height: 1.5;
  margin-bottom: 1.5rem;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.product-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.product-stock {
  color: #718096;
  font-size: 0.875rem;
  font-weight: 500;
}

.product-actions {
  display: flex;
  gap: 0.75rem;
}

.edit-button,
.delete-button {
  padding: 0.5rem 1rem;
  border-radius: 6px;
  font-size: 0.875rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  border: 1px solid transparent;
}

.edit-button {
  background-color: #ebf4ff;
  color: #4c51bf;
}

.edit-button:hover {
  background-color: #e2e8f0;
}

.delete-button {
  background-color: #fff5f5;
  color: #e53e3e;
}

.delete-button:hover {
  background-color: #fed7d7;
}

/* Empty State */
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 4rem 2rem;
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05),
    0 2px 4px -1px rgba(0, 0, 0, 0.02);
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

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(4px);
}

.modal-content {
  background-color: white;
  border-radius: 12px;
  width: 90%;
  max-width: 600px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1),
    0 10px 10px -5px rgba(0, 0, 0, 0.04);
  animation: modalFadeIn 0.3s ease;
}

@keyframes modalFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  border-bottom: 1px solid #edf2f7;
}

.modal-header h3 {
  font-size: 1.375rem;
  font-weight: 600;
  color: #2d3748;
  margin: 0;
}

.close-button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;
  color: #a0aec0;
  transition: color 0.2s ease;
}

.close-button:hover {
  color: #718096;
}

.close-button svg {
  width: 1.5rem;
  height: 1.5rem;
}

.modal-body {
  padding: 1.5rem;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
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

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.file-upload {
  position: relative;
}

.file-upload input[type="file"] {
  position: absolute;
  left: 0;
  top: 0;
  opacity: 0;
  width: 100%;
  height: 100%;
  cursor: pointer;
}

.upload-label {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.85rem;
  background-color: #f7fafc;
  color: #4a5568;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s ease;
  border: 1px dashed #cbd5e0;
}

.upload-label:hover {
  background-color: #edf2f7;
  border-color: #a0aec0;
}

.upload-icon {
  width: 1.25rem;
  height: 1.25rem;
  color: #718096;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  padding: 1.5rem;
  border-top: 1px solid #edf2f7;
}

.cancel-button,
.submit-button {
  padding: 0.85rem 1.5rem;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
}

.cancel-button {
  background-color: white;
  color: #4a5568;
  border: 1px solid #e2e8f0;
}

.cancel-button:hover {
  background-color: #f7fafc;
  border-color: #cbd5e0;
}

.submit-button {
  background-color: #4c51bf;
  color: white;
  border: none;
}

.submit-button:hover {
  background-color: #434190;
}

/* Responsive Adjustments */
@media (max-width: 768px) {
  .product-manager-container {
    padding: 1.5rem;
  }

  .product-manager-actions {
    flex-direction: column;
  }

  .search-container {
    width: 100%;
    max-width: none;
  }

  .add-product-button {
    width: 100%;
    justify-content: center;
  }

  .form-row {
    grid-template-columns: 1fr;
  }

  .product-grid {
    grid-template-columns: 1fr;
  }
}
</style>

<script>
import axios from "../../axios";
import EmployeeNav from "./EmployeeNav.vue";

export default {
  components: {
    EmployeeNav,
  },

  data() {
    return {
      products: [],
      showModal: false,
      isEditing: false,
      selectedId: null,
      form: {
        name: "",
        description: "",
        price: "",
        stock: "",
        image: null,
      },
      searchQuery: "",
    };
  },
  computed: {
    filteredProducts() {
      return this.products.filter(
        (product) =>
          product.name.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          product.description
            .toLowerCase()
            .includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    fetchProducts() {
      axios.get("products/").then((res) => {
        this.products = res.data;
      });
    },
    logout() {
      localStorage.removeItem("token");
      localStorage.removeItem("userType");
      this.$router.push("/login");
    },
    openCreateForm() {
      this.resetForm();
      this.isEditing = false;
      this.showModal = true;
    },
    openEditForm(product) {
      this.form = { ...product, image: null };
      this.selectedId = product.id;
      this.isEditing = true;
      this.showModal = true;
    },
    openTransaction() {
      this.$router.push("transactions");
    },
    handleImage(e) {
      this.form.image = e.target.files[0];
    },
    closeModal() {
      this.showModal = false;
    },
    resetForm() {
      this.form = {
        name: "",
        description: "",
        price: "",
        stock: "",
        image: null,
      };
      this.selectedId = null;
    },
    submitProduct() {
      const formData = new FormData();
      formData.append("name", this.form.name);
      formData.append("description", this.form.description);
      formData.append("price", this.form.price);
      formData.append("stock", this.form.stock);
      if (this.form.image) {
        formData.append("image", this.form.image);
      }

      if (this.isEditing) {
        axios.put(`products/${this.selectedId}/`, formData).then(() => {
          this.fetchProducts();
          this.closeModal();
        });
      } else {
        axios.post("products/", formData).then(() => {
          this.fetchProducts();
          this.closeModal();
        });
      }
    },
    deleteProduct(id) {
      if (confirm("Are you sure you want to delete this product?")) {
        axios
          .delete(`products/${id}/`)
          .then(() => {
            this.fetchProducts();
          })
          .catch((err) => {
            alert(err.response.data.error || "Failed to delete product.");
          });
      }
    },
  },
  mounted() {
    this.fetchProducts();
  },
};
</script>
