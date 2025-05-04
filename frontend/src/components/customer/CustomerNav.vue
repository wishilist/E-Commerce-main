<template>
  <nav class="customer-navbar">
    <div class="navbar-container">
      <span class="app-logo">ShopHub</span>

      <div class="nav-links">
        <!-- Customer Links -->
        <template v-if="isCustomer">
          <router-link class="nav-link" to="/customer/products">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="currentColor"
              class="nav-icon"
            >
              <path
                d="M3.375 3C2.339 3 1.5 3.84 1.5 4.875v.75c0 1.036.84 1.875 1.875 1.875h17.25c1.035 0 1.875-.84 1.875-1.875v-.75C22.5 3.839 21.66 3 20.625 3H3.375z"
              />
              <path
                fill-rule="evenodd"
                d="M3.087 9l.54 9.176A3 3 0 006.62 21h10.757a3 3 0 002.995-2.824L20.913 9H3.087zm6.133 2.845a.75.75 0 011.06 0l1.72 1.72 1.72-1.72a.75.75 0 111.06 1.06l-1.72 1.72 1.72 1.72a.75.75 0 11-1.06 1.06L12 15.685l-1.72 1.72a.75.75 0 11-1.06-1.06l1.72-1.72-1.72-1.72a.75.75 0 010-1.06z"
                clip-rule="evenodd"
              />
            </svg>
            Products
          </router-link>
          <router-link class="nav-link" to="/customer/cart">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="currentColor"
              class="nav-icon"
            >
              <path
                d="M2.25 2.25a.75.75 0 000 1.5h1.386c.17 0 .318.114.362.278l2.558 9.592a3.752 3.752 0 00-2.806 3.63c0 .414.336.75.75.75h15.75a.75.75 0 000-1.5H5.378A2.25 2.25 0 017.5 15h11.218a.75.75 0 00.674-.421 60.358 60.358 0 002.96-7.228.75.75 0 00-.525-.965A60.864 60.864 0 005.68 4.509l-.232-.867A1.875 1.875 0 003.636 2.25H2.25zM3.75 20.25a1.5 1.5 0 113 0 1.5 1.5 0 01-3 0zM16.5 20.25a1.5 1.5 0 113 0 1.5 1.5 0 01-3 0z"
              />
            </svg>
            Cart
          </router-link>
        </template>

        <button class="logout-button" @click.prevent="confirmLogout">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
            fill="currentColor"
            class="nav-icon"
          >
            <path
              fill-rule="evenodd"
              d="M7.5 3.75A1.5 1.5 0 006 5.25v13.5a1.5 1.5 0 001.5 1.5h6a1.5 1.5 0 001.5-1.5V15a.75.75 0 011.5 0v3.75a3 3 0 01-3 3h-6a3 3 0 01-3-3V5.25a3 3 0 013-3h6a3 3 0 013 3V9A.75.75 0 0115 9V5.25a1.5 1.5 0 00-1.5-1.5h-6zm10.72 4.72a.75.75 0 011.06 0l3 3a.75.75 0 010 1.06l-3 3a.75.75 0 11-1.06-1.06l1.72-1.72H9a.75.75 0 010-1.5h10.94l-1.72-1.72a.75.75 0 010-1.06z"
              clip-rule="evenodd"
            />
          </svg>
          Logout
        </button>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { computed } from "vue";
import { useRouter } from "vue-router";
import Swal from "sweetalert2";

const router = useRouter();

const isLoggedIn = computed(() => !!localStorage.getItem("token"));
const userType = computed(() => localStorage.getItem("userType"));

const isCustomer = computed(
  () => isLoggedIn.value && userType.value === "customer"
);

const confirmLogout = () => {
  Swal.fire({
    title: "Ready to leave?",
    text: "Are you sure you want to log out?",
    icon: "question",
    showCancelButton: true,
    confirmButtonColor: "#4c51bf",
    cancelButtonColor: "#a0aec0",
    confirmButtonText: "Yes, log me out",
    cancelButtonText: "Stay logged in",
    background: "#ffffff",
    backdrop: `
      rgba(76, 81, 191, 0.1)
      url("/images/nyan-cat.gif")
      left top
      no-repeat
    `,
  }).then((result) => {
    if (result.isConfirmed) {
      logout();
    }
  });
};

const logout = () => {
  localStorage.removeItem("token");
  localStorage.removeItem("userType");
  router.push("/login");
};
</script>

<style scoped>
.customer-navbar {
  position: sticky;
  top: 0;
  z-index: 50;
  background-color: white;
  border-bottom: 1px solid #edf2f7;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
  backdrop-filter: blur(8px);
}

.navbar-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1400px;
  margin: 0 auto;
  padding: 1rem 2rem;
}

.app-logo {
  font-family: "Inter", system-ui, -apple-system, sans-serif;
  font-weight: 700;
  font-size: 1.5rem;
  color: #4c51bf;
  letter-spacing: -0.5px;
}

.nav-links {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-family: "Inter", system-ui, -apple-system, sans-serif;
  font-weight: 500;
  color: #4a5568;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  transition: all 0.2s ease;
  text-decoration: none;
}

.nav-link:hover {
  color: #4c51bf;
  background-color: #f0f5ff;
}

.nav-link.router-link-active {
  color: #4c51bf;
  background-color: #ebf4ff;
  font-weight: 600;
}

.nav-icon {
  width: 1.25rem;
  height: 1.25rem;
}

.logout-button {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-family: "Inter", system-ui, -apple-system, sans-serif;
  font-weight: 500;
  color: #e53e3e;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  transition: all 0.2s ease;
  background: none;
  border: none;
  cursor: pointer;
}

.logout-button:hover {
  color: #c53030;
  background-color: #fff5f5;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .navbar-container {
    padding: 1rem;
  }

  .nav-links {
    gap: 0.75rem;
  }

  .nav-link,
  .logout-button {
    padding: 0.5rem;
    font-size: 0.875rem;
  }

  .nav-icon {
    width: 1rem;
    height: 1rem;
  }
}
</style>
