<template>
  <nav class="app-navbar">
    <div class="navbar-container">
      <span class="app-logo">ShopHub</span>

      <div class="nav-links">
        <!-- Employee Links -->
        <template v-if="isEmployee">
          <router-link class="nav-link" to="/employee/products">
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
          <router-link class="nav-link" to="/employee/transactions">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="currentColor"
              class="nav-icon"
            >
              <path d="M12 7.5a2.25 2.25 0 100 4.5 2.25 2.25 0 000-4.5z" />
              <path
                fill-rule="evenodd"
                d="M1.5 4.875C1.5 3.839 2.34 3 3.375 3h17.25c1.035 0 1.875.84 1.875 1.875v9.75c0 1.036-.84 1.875-1.875 1.875H3.375A1.875 1.875 0 011.5 14.625v-9.75zM8.25 9.75a3.75 3.75 0 117.5 0 3.75 3.75 0 01-7.5 0zM18.75 9a.75.75 0 00-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 00.75-.75V9.75a.75.75 0 00-.75-.75h-.008zM4.5 9.75A.75.75 0 015.25 9h.008a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75H5.25a.75.75 0 01-.75-.75V9.75z"
                clip-rule="evenodd"
              />
              <path
                d="M2.25 18a.75.75 0 000 1.5c5.4 0 10.63.722 15.6 2.075 1.19.324 2.4-.558 2.4-1.82V18.75a.75.75 0 00-.75-.75H2.25z"
              />
            </svg>
            Transactions
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

// Computed properties for login state and user type
const isLoggedIn = computed(() => !!localStorage.getItem("token"));
const userType = computed(() => localStorage.getItem("userType"));

// Only show employee links if logged in and user is an employee
const isEmployee = computed(
  () => isLoggedIn.value && userType.value === "employee"
);

// Logout logic with SweetAlert2 confirmation
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
.app-navbar {
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

/* Fallback font */
@supports not (font-family: "Inter") {
  .app-logo,
  .nav-link,
  .logout-button {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      sans-serif;
  }
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
