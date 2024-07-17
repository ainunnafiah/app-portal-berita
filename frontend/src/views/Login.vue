<template>
  <!-- <AuthLayout> -->
    <div class="login-card">
      <div class="card p-4 shadow-lg rounded">
        <h6 class="text-center mb-4">
          Login ke Berita.com dengan email dan kata sandi Anda!
        </h6>
        <form @submit.prevent="login" class="mb-3">
          <input
            class="form-control"
            type="text"
            placeholder="Email"
            v-model="email"
          />
          <input
            class="form-control mt-3"
            type="password"
            placeholder="Password"
            v-model="password"
          />
          <div v-if="errorMessage" class="alert alert-danger mt-3">
            {{ errorMessage }}
          </div>
          <div class="d-flex justify-content-between align-items-center mt-3">
            <div class="form-check">
              <input
                class="form-check-input"
                type="checkbox"
                value=""
                id="rememberMe"
              />
              <label class="form-check-label" for="rememberMe">
                Remember me
              </label>
            </div>
            <div class="text-danger cursor-pointer" @click="forgotPassword">
              Forgot Password?
            </div>
          </div>
          <button type="submit" class="btn btn-primary mt-4">Login</button>
        </form>
        <h6 class="text-center my-3">atau</h6>
        <div class="d-flex justify-content-center">
          <img
            src="../assets/icon/google.svg"
            alt="Google Logo"
            class="google-logo"
            @click="loginWithGoogle"
          />
        </div>
        <div class="d-flex justify-content-center mt-3">
          <span class="me-2">Belum memiliki akun?</span>
          <span class="text-danger cursor-pointer" @click="register">Daftar!</span>
        </div>
      </div>
    </div>
  <!-- </AuthLayout> -->
</template>

<script>
import axios from "../../services/axios";
import AuthLayout from "../components/Auth/AuthLayout.vue";

export default {
  name: "Login",
  components: {
    AuthLayout,
  },
  data() {
    return {
      email: "",
      password: "",
      errorMessage: "",
    };
  },
  methods: {
    validateEmail(email) {
      const re = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;
      return re.test(email);
    },
    async login() {
      if (!this.validateEmail(this.email)) {
        this.errorMessage = "Email tidak valid";
        return;
      }

      try {
        const response = await axios.post(
          "http://localhost:5000/api/Auth/Login",
          {
            email: this.email,
            password: this.password,
          }
        );

        console.log(response.data); // Log the response data

        if (response.data.success) {
          const userRole = response.data.role;
          const token = response.data.token;
          localStorage.setItem("userRole", userRole); // Store the role in local storage
          localStorage.setItem("token", token); // Store the token in local storage

          // Log to check if the token is set correctly
          console.log("Stored token:", localStorage.getItem("token"));

          if (userRole === "admin") {
            this.$router.push({ name: "Admin" });
          } else if (userRole === "author") {
            this.$router.push({ name: "Contributor" });
          } else {
            this.$router.push({ name: "LandingPage" });
          }
        } else {
          this.errorMessage = response.data.msg || "Unknown error";
        }
      } catch (error) {
        console.error("There was an error logging in:", error);
        if (error.response && error.response.data && error.response.data.msg) {
          this.errorMessage = error.response.data.msg;
        } else {
          this.errorMessage = "An error occurred. Please try again.";
        }
      }
    },
    forgotPassword() {
      this.$router.push({ name: "ForgotPassword" });
    },
    register() {
      this.$router.push({ name: "Register" });
    },
    loginWithGoogle() {
      // Redirect to Google OAuth URL
      window.location.href = "http://localhost:5000/auth/google";
    },
  },
};
</script>

<style scoped>
.login-card {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; /* Center the card vertically */
  background: url(../assets/AuthBlueBg.svg);
}

.card {
  max-width: 400px;
  width: 100%;
  padding: 20px;
  border: none;
}

.form-control {
  width: 100%;
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ddd;
  border-radius: 5px;
}

.alert {
  padding: 10px;
  border-radius: 5px;
}

.form-check-input {
  margin-right: 5px;
}

.google-logo {
  width: 30px;
  height: 30px;
  cursor: pointer;
}

.btn {
  width: 100%;
  padding: 10px;
  margin-top: 10px;
  font-size: 1rem;
}

.text-danger {
  cursor: pointer;
}
</style>
