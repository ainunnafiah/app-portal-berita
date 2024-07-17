<template>
  <div class="register-container">
    <div class="register-card">
      <h6 class="text-center mb-4">
        Daftar ke Berita.com dan temukan berita terkini!
      </h6>
      <div v-if="message" :class="`alert ${messageType}`" role="alert">
        {{ message }}
      </div>
      <form @submit.prevent="registerUser">
        <input
          class="form-control"
          type="text"
          placeholder="Username"
          v-model="username"
        />
        <input
          class="form-control mt-2"
          type="text"
          placeholder="Email"
          v-model="email"
        />
        <input
          class="form-control mt-2"
          type="password"
          placeholder="Kata Sandi"
          v-model="password"
        />
        <input
          class="form-control mt-2"
          type="password"
          placeholder="Konfirmasi Kata Sandi"
          v-model="confPassword"
        />
        <div class="font-size-small mt-1 mb-2">
          Minimal 8 karakter dengan kombinasi huruf, angka, dan simbol
        </div>
        <div class="form-group">
          <label for="roleSelect">Role:</label>
          <select class="form-control mt-2" id="roleSelect" v-model="role">
            <option value="user">User</option>
            <option value="author">Author</option>
          </select>
        </div>
        <button type="submit" class="btn btn-primary mt-4">Register</button>
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
        <span class="me-2">Sudah memiliki akun?</span>
        <span class="text-danger cursor-pointer" @click="login">Login!</span>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Register",
  data() {
    return {
      username: "",
      email: "",
      password: "",
      confPassword: "",
      role: "user", // Default role is "user"
      message: "", // Message to show success or error
      messageType: "", // Type of message (success or error)
    };
  },
  methods: {
    async registerUser() {
      // Validasi bidang form
      if (
        !this.username ||
        !this.email ||
        !this.password ||
        !this.confPassword
      ) {
        this.showMessage("Semua bidang harus diisi", "alert-danger");
        return;
      }

      // Validasi konfirmasi password
      if (this.password !== this.confPassword) {
        this.showMessage("Konfirmasi kata sandi tidak sesuai", "alert-danger");
        return;
      }

      try {
        const response = await axios.post("http://localhost:5000/Register", {
          username: this.username,
          email: this.email,
          password: this.password,
          confPassword: this.confPassword,
          role: this.role,
        });

        console.log(response.data); // Log the response data

        if (response.data.success) {
          this.showMessage(response.data.msg, "alert-success");
          setTimeout(() => {
            this.$router.push({ name: "Login" }); // Redirect to Login page
          }, 2000); // Wait for 2 seconds before redirecting
        } else {
          this.showMessage(
            "Registration failed: " + (response.data.msg || "Unknown error"),
            "alert-danger"
          );
        }
      } catch (error) {
        console.error("There was an error registering:", error);
        if (error.response && error.response.data && error.response.data.msg) {
          this.showMessage(
            "Registration failed: " + error.response.data.msg,
            "alert-danger"
          );
        } else {
          this.showMessage(
            "An error occurred. Please try again.",
            "alert-danger"
          );
        }
      }
    },
    showMessage(msg, type) {
      this.message = msg;
      this.messageType = type;
    },
    login() {
      this.$router.push({ name: "Login" });
    },
    loginWithGoogle() {
      window.location.href = "http://localhost:5000/auth/google";
    },
  },
};
</script>

<style scoped>
.register-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: url(../assets/AuthBlueBg.svg); /* Blue background, not too dark */
}

.register-card {
  max-width: 400px;
  width: 100%;
  padding: 20px;
  background: rgba(255, 255, 255, 0.9); /* Semi-transparent white background */
  border-radius: 10px;
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
}

.form-control {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
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

.font-size-small {
  font-size: 0.8rem;
}
</style>
