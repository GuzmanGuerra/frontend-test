<template>
  <form class="form-signin" @submit.prevent="onSignIn" novalidate>
    <img class="mb-4" src="../assets/logo.png" alt="" width="100" height="100">
    <h1 class="h3 mb-3 font-weight-normal">Please sign in</h1>
    <div v-if="invalidAuth" class="mb-2 text-danger">{{ "Usuario o contraseña incorrecta " }}</div>
    <div class="row">
      <input type="text" id="username" class="form-control mb-2 col-10" :class="{ 'is-invalid': usernameError }"
        placeholder="Username" required v-model="username">
      <div v-if="usernameError" class="invalid-feedback">
        {{ usernameError }}
      </div>
    </div>
    <div class="row">
      <input :type="showPassword ? 'text' : 'password'" id="inputPassword" class="form-control mb-2"
        :class="{ 'is-invalid': passwordError }" placeholder="Password" required v-model="password">
      <div v-if="passwordError" class="invalid-feedback">
        {{ passwordError }}
      </div>
    </div>
    <div class="row">
      <div class="form-check col-auto">
        <input class="form-check-input" type="checkbox" id="hola" v-model="showPassword">
        <label class="form-check-label" for="hola"> Mostrar constraseña</label>
      </div>
    </div>

    <button class="btn btn-lg btn-outline-dark btn-block mt-2" type="submit">
      <span>Sign in</span>

      <!-- Loader -->
      <span v-if="isLoading" class="spinner-border spinner-border-sm mx-2" role="status" aria-hidden="false"></span>
    </button>
  </form>
</template>

<script>
import axios from 'axios';
import { postLoginEndpoint, tokenStorageName } from '@/helpers/constants';

export default {
  name: 'Login',
  data() {
    return {
      username: "",
      password: "",
      showPassword: false,
      isLoading: false,
      invalidAuth: false,
      usernameError: null,
      passwordError: null,
    }
  },
  methods: {
    async onSignIn(event) {

      this.invalidAuth = false
      this.usernameError = null;
      this.passwordError = null;

      if (!this.username.trim()) {
        this.usernameError = "El nombre de usuario es obligatorio.";
      }
      if (!this.password.trim()) {
        this.passwordError = "La contraseña es obligatoria.";
      }
      if (this.usernameError || this.passwordError) {
        return;
      }

      try {
        this.isLoading = true
        const response = await axios.post(postLoginEndpoint,
          {
            username: this.username,
            password: this.password
          },
          {
            headers: {
              'Content-Type': 'application/json'
            }
          })
        const { status, data } = response
        if (status === 200) {
          //Persistimos el token
          localStorage.setItem(tokenStorageName, data.token)
          //Redirigimos a la vista principal
          this.$router.push("/")
        }
      } catch (error) {
        this.invalidAuth = true
      }
      finally {
        this.isLoading = false
      }

    }
  }
}
</script>

<style scoped>
.form-signin {
  width: 100%;
  max-width: 330px;
  padding: 15px;
  margin: 0 auto;
}
</style>
