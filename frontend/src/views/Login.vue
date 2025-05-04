<template>
  <div class="login-container">
    <div class="login-card">
      <div class="login-header">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          fill="currentColor"
          class="login-icon"
        >
          <path
            fill-rule="evenodd"
            d="M18.685 19.097A9.723 9.723 0 0021.75 12c0-5.385-4.365-9.75-9.75-9.75S2.25 6.615 2.25 12a9.723 9.723 0 003.065 7.097A9.716 9.716 0 0012 21.75a9.716 9.716 0 006.685-2.653zm-12.54-1.285A7.486 7.486 0 0112 15a7.486 7.486 0 015.855 2.812A8.224 8.224 0 0112 20.25a8.224 8.224 0 01-5.855-2.438zM15.75 9a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0z"
            clip-rule="evenodd"
          />
        </svg>
        <h2 class="login-title">Welcome Back</h2>
        <p class="login-subtitle">Sign in to your account</p>
      </div>

      <form @submit.prevent="login" class="login-form">
        <div class="form-group">
          <input
            v-model="username"
            type="text"
            class="form-input"
            placeholder=" "
            required
          />
          <label class="form-label">Username</label>
        </div>

        <div class="form-group">
          <input
            v-model="password"
            type="password"
            class="form-input"
            placeholder=" "
            required
          />
          <label class="form-label">Password</label>
        </div>

        <button class="login-button">
          <span>Continue</span>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
            fill="currentColor"
            class="button-icon"
          >
            <path
              fill-rule="evenodd"
              d="M3 10a.75.75 0 01.75-.75h10.638L10.23 5.29a.75.75 0 111.04-1.08l5.5 5.25a.75.75 0 010 1.08l-5.5 5.25a.75.75 0 11-1.04-1.08l4.158-3.96H3.75A.75.75 0 013 10z"
              clip-rule="evenodd"
            />
          </svg>
        </button>
      </form>

      <div class="login-footer">
        <p>
          Don't have an account?
          <router-link to="/register" class="footer-link">Register</router-link>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "../axios";
import { useRouter } from "vue-router";

const username = ref("");
const password = ref("");
const router = useRouter();

const login = async () => {
  try {
    const res = await axios.post("/login/", {
      username: username.value,
      password: password.value,
    });

    localStorage.setItem("token", res.data.token);
    axios.defaults.headers.common["Authorization"] = `Token ${res.data.token}`;

    const userRes = await axios.get("/users/");
    localStorage.setItem("userType", userRes.data.user_type);

    if (userRes.data.user_type === "employee") {
      router.push("/employee/products");
    } else {
      router.push("/customer/products");
    }
  } catch (err) {
    alert("Invalid credentials or error during login.");
  }
};
</script>

<style scoped>
.login-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f8fafc;
  padding: 2rem;
}

.login-card {
  width: 100%;
  max-width: 28rem;
  background-color: #ffffff;
  border-radius: 0.75rem;
  box-shadow: 0 4px 12px rgba(59, 130, 246, 0.1);
  padding: 2.5rem;
  border: 1px solid #e0e7ff;
}

.login-header {
  text-align: center;
  margin-bottom: 2rem;
}

.login-icon {
  width: 3rem;
  height: 3rem;
  color: #3b82f6;
  margin-bottom: 1rem;
}

.login-title {
  font-family: "Inter", sans-serif;
  font-size: 1.75rem;
  font-weight: 700;
  color: #1e3a8a;
  margin-bottom: 0.5rem;
}

.login-subtitle {
  color: #64748b;
  font-family: "Inter", sans-serif;
  font-size: 0.95rem;
}

.login-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-group {
  position: relative;
}

.form-input {
  width: 100%;
  padding: 1rem;
  border: 1px solid #c7d2fe;
  border-radius: 0.5rem;
  font-size: 1rem;
  transition: all 0.2s;
  background-color: #f8fafc;
  font-family: "Inter", sans-serif;
}

.form-input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.form-input:focus + .form-label,
.form-input:not(:placeholder-shown) + .form-label {
  transform: translateY(-1.5rem) scale(0.875);
  background-color: white;
  padding: 0 0.25rem;
  color: #3b82f6;
}

.form-label {
  position: absolute;
  left: 1rem;
  top: 1rem;
  color: #64748b;
  pointer-events: none;
  transition: all 0.2s;
  transform-origin: left center;
  font-family: "Inter", sans-serif;
  font-weight: 500;
}

.login-button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 1rem;
  background-color: #3b82f6;
  color: white;
  border: none;
  border-radius: 0.5rem;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
  margin-top: 0.5rem;
  font-family: "Inter", sans-serif;
}

.login-button:hover {
  background-color: #2563eb;
  transform: translateY(-1px);
}

.button-icon {
  width: 1.25rem;
  height: 1.25rem;
  margin-left: 0.5rem;
}

.login-footer {
  margin-top: 1.5rem;
  text-align: center;
  color: #64748b;
  font-family: "Inter", sans-serif;
  font-size: 0.9rem;
}

.footer-link {
  color: #3b82f6;
  text-decoration: none;
  font-weight: 600;
  transition: color 0.2s;
}

.footer-link:hover {
  color: #1d4ed8;
  text-decoration: underline;
}

/* Fallback fonts */
@supports not (font-family: "Inter") {
  .login-title,
  .login-subtitle,
  .form-input,
  .form-label,
  .login-button,
  .login-footer {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      sans-serif;
  }
}
</style>
