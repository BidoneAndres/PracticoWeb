<template>
  <v-app>
    <v-main>
      <div class="login-background d-flex justify-center align-center">
        <v-card class="login-card pa-8" elevation="12">
          <v-card-title class="justify-center">
            <v-icon size="48" color="primary">mdi-account-circle</v-icon>
          </v-card-title>
          <v-card-subtitle class="text-center mb-6">
            Bienvenido, inicia sesión
          </v-card-subtitle>

          <v-form ref="loginForm" v-model="valid">
            <v-text-field
              v-model="email"
              label="Correo electrónico"
              type="email"
              prepend-inner-icon="mdi-email"
              :rules="emailRules"
              required
            />

            <v-text-field
              v-model="password"
              label="Contraseña"
              type="password"
              prepend-inner-icon="mdi-lock"
              :rules="passwordRules"
              required
            />

            <v-btn
              class="mt-4 login-btn"
              color="primary"
              large
              block
              @click="submitLogin"
            >
              Iniciar Sesión
            </v-btn>
          </v-form>

          <v-card-subtitle class="text-center mt-4">
            ¿No tienes cuenta?
            <a href="#" @click.prevent="$emit('show-register')">Regístrate</a>
          </v-card-subtitle>
        </v-card>
      </div>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref } from 'vue'

const email = ref('')
const password = ref('')
const valid = ref(false)
const loginForm = ref(null)

const emit = defineEmits(['login-success', 'show-register'])

const emailRules = [
  v => !!v || 'El correo es requerido',
  v => /.+@.+\..+/.test(v) || 'El correo debe ser válido'
]

const passwordRules = [
  v => !!v || 'La contraseña es requerida',
  v => v.length >= 6 || 'La contraseña debe tener al menos 6 caracteres'
]

const submitLogin = () => {
  if (!loginForm.value.validate()) return

  const users = JSON.parse(localStorage.getItem('users') || '[]')
  const user = users.find(u => u.email === email.value && u.password === password.value)

  if (user) {
    // 🔑 guardar el usuario en localStorage
    localStorage.setItem('currentUser', JSON.stringify(user))

    emit('login-success', user) // aviso al App.vue
  } else {
    alert('Usuario o contraseña incorrectos')
  }
}

</script>

<style scoped>
.login-background {
  height: 100vh;
  background: linear-gradient(135deg, #6a11cb, #2575fc);
}

.login-card {
  width: 400px;
  border-radius: 16px;
  padding: 32px;
}

.login-btn {
  transition: all 0.3s ease;
}
.login-btn:hover {
  transform: scale(1.05);
}
a {
  color: #1976d2;
  text-decoration: none;
}
a:hover {
  text-decoration: underline;
}

@media (max-width: 500px) {
  .login-card {
    width: 90%;
    padding: 24px;
  }
}
</style>
