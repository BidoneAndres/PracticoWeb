<template>
  <v-app>
    <v-main>
      <div class="login-background d-flex justify-center align-center">
        <v-card class="login-card pa-8" elevation="12">
          <v-card-title class="justify-center">
            <v-icon size="48" color="success">mdi-account-plus</v-icon>
          </v-card-title>
          <v-card-subtitle class="text-center mb-6">
            Crea tu cuenta
          </v-card-subtitle>

          <v-form ref="registerForm" v-model="valid">
            <v-text-field
              v-model="username"
              label="Nombre de usuario"
              prepend-inner-icon="mdi-account"
              :rules="usernameRules"
              required
            />

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

            <v-text-field
              v-model="confirmPassword"
              label="Confirmar contraseña"
              type="password"
              prepend-inner-icon="mdi-lock-check"
              :rules="confirmRules"
              required
            />

            <v-btn
              class="mt-4 login-btn"
              color="success"
              large
              block
              @click="submitRegister"
            >
              Registrarse
            </v-btn>

            <v-btn text block class="mt-2" @click="$emit('back-to-login')">
              Volver al login
            </v-btn>
          </v-form>
        </v-card>
      </div>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref } from 'vue'

const username = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const valid = ref(false)
const registerForm = ref(null)

const emit = defineEmits(['register-success', 'back-to-login'])

const usernameRules = [v => !!v || 'El nombre es requerido']
const emailRules = [
  v => !!v || 'El correo es requerido',
  v => /.+@.+\..+/.test(v) || 'Correo inválido'
]
const passwordRules = [
  v => !!v || 'La contraseña es requerida',
  v => v.length >= 6 || 'Mínimo 6 caracteres'
]
const confirmRules = [
  v => v === password.value || 'Las contraseñas no coinciden'
]

const submitRegister = () => {
  if (!registerForm.value.validate()) return

  let users = JSON.parse(localStorage.getItem('users') || '[]')
  if (users.find(u => u.email === email.value)) {
    alert('El correo ya está registrado')
    return
  }

  const newUser = { username: username.value, email: email.value, password: password.value }

  users.push(newUser)
  localStorage.setItem('users', JSON.stringify(users))


  localStorage.setItem('currentUser', JSON.stringify(newUser))

 
  emit('register-success', newUser)
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
@media (max-width: 500px) {
  .login-card {
    width: 90%;
    padding: 24px;
  }
}
</style>
