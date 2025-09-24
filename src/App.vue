<template>
  <v-app>
    <v-btn
      v-if="isLoggedIn"
      class="boton"
     
    >
     Bienvenido {{ currentUser?.username || currentUser?.email }}!!!
    </v-btn>
    <v-main>
      <div class="login-background d-flex justify-center align-center">
        <transition name="fade" mode="out-in">
          <Login
            v-if="!isLoggedIn && !showRegister"
            @login-success="handleLoginSuccess"
            @show-register="showRegister = true"
            key="login"
          />

          <Register
            v-else-if="!isLoggedIn && showRegister"
            @register-success="handleRegisterSuccess"
            @back-to-login="showRegister = false"
            key="register"
          />

          <ProductList
            v-else
            :products="products"
            @add-to-cart="handleAddToCart"
            key="products"
          />
        </transition>
        <v-btn
          v-if="isLoggedIn"
          style="position: fixed; color: red; bottom: 25px; right: 25px; z-index: 9999;"
          @click="logout"
        >
        Desea salir {{ currentUser?.username || currentUser?.email }}??
        </v-btn>
      </div>
      
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import ProductList from './components/ProductList.vue'
import Login from './components/Login.vue'
import Register from './components/Register.vue'

const isLoggedIn = ref(false)
const showRegister = ref(false)
const currentUser = ref(null)

onMounted(() => {
  const savedUser = localStorage.getItem('currentUser')
  if (savedUser) {
    currentUser.value = JSON.parse(savedUser)
    isLoggedIn.value = true
  }
})

const products = ref([
  {
    id: 1, 
    nombre: 'Notebook Lenovo', 
    precio: 1200, 
    stock: 5, 
    imagen: '/Lenovo-Thinkbook-C-i5-3.jpg',
    descripcion: 'Notebook Lenovo Thinkbook con procesador Intel i5, ideal para trabajo y estudio.'
  },
  { 
    id: 2, 
    nombre: 'Mouse Logitech', 
    precio: 25, 
    stock: 0, 
    imagen: '/71wmuZLI6xL._AC_SL1500_.jpg',
    descripcion: 'Mouse inalámbrico Logitech de alta precisión, ergonómico y liviano.'
  },
  { 
    id: 3, 
    nombre: 'Teclado Mecánico Redragon', 
    precio: 45, 
    stock: 10, 
    imagen: '/23-455-008-Z01.jpg',
    descripcion: 'Teclado mecánico retroiluminado con switches azules y diseño gamer.'
  },
  { 
    id: 4, 
    nombre: 'Monitor Samsung 24"', 
    precio: 200, 
    stock: 2, 
    imagen: '/OIP.webp',
    descripcion: 'Monitor LED Full HD de 24 pulgadas con panel IPS y bordes delgados.'
  },
  { 
    id: 5, 
    nombre: 'Auriculares HyperX Cloud II', 
    precio: 95, 
    stock: 8, 
    imagen: 'https://tse2.mm.bing.net/th/id/OIP.2cGTJosx12DWLL7rvwDfDwHaHa?rs=1&pid=ImgDetMain&o=7&rm=3',
    descripcion: 'Auriculares gaming con sonido envolvente 7.1 y micrófono desmontable.'
  },
  { 
    id: 6, 
    nombre: 'Impresora HP DeskJet', 
    precio: 150, 
    stock: 4, 
    imagen: 'https://www.hp.com/es-es/shop/Html/Merch/Images/c06210365_1750x1285.jpg',
    descripcion: 'Impresora multifunción compacta con conexión Wi-Fi y bajo consumo.'
  },
  { 
    id: 7, 
    nombre: 'Tablet Samsung Galaxy Tab A7', 
    precio: 300, 
    stock: 7, 
    imagen: 'https://tse1.mm.bing.net/th/id/OIP.UvBeCQSgAOqYVoW2jWOtKAHaHa?rs=1&pid=ImgDetMain&o=7&rm=3',
    descripcion: 'Tablet de 10.4 pulgadas con pantalla brillante y batería de larga duración.'
  },
  { 
    id: 8, 
    nombre: 'Smartwatch Amazfit Bip U', 
    precio: 65, 
    stock: 12, 
    imagen: 'https://m.media-amazon.com/images/I/617RQK5jKDL._AC_SL1500_.jpg',
    descripcion: 'Reloj inteligente con monitoreo de salud, GPS y más de 60 modos deportivos.'
  },
  { 
    id: 9, 
    nombre: 'Parlante Bluetooth JBL Flip 5', 
    precio: 110, 
    stock: 9, 
    imagen: 'https://m.media-amazon.com/images/I/71o8Q5XJS5L._AC_SL1500_.jpg',
    descripcion: 'Altavoz portátil resistente al agua con graves potentes y 12h de autonomía.'
  },
  { 
    id: 10, 
    nombre: 'Disco SSD Kingston 480GB', 
    precio: 55, 
    stock: 15, 
    imagen: 'https://tse4.mm.bing.net/th/id/OIP.r6jhvzVJek_4uNezTg9rxwHaE3?rs=1&pid=ImgDetMain&o=7&rm=3',
    descripcion: 'Unidad de estado sólido rápida y confiable para mejorar el rendimiento del PC.'
  },
  { 
    id: 11, 
    nombre: 'Memoria RAM Corsair Vengeance 16GB', 
    precio: 70, 
    stock: 6, 
    imagen: 'https://mla-s1-p.mlstatic.com/938220-MLA40928711013_022020-F.jpg',
    descripcion: 'Módulo de memoria DDR4 de alto rendimiento, ideal para gaming y multitarea.'
  },
  { 
    id: 12, 
    nombre: 'Placa de Video NVIDIA RTX 3060', 
    precio: 450, 
    stock: 3, 
    imagen: 'https://tse3.mm.bing.net/th/id/OIP.qEqG2jNsriXyH29gh01MjAHaHa?w=1000&h=1000&rs=1&pid=ImgDetMain&o=7&rm=3',
    descripcion: 'Tarjeta gráfica con 12GB GDDR6, soporte para Ray Tracing y DLSS.'
  },
  { 
    id: 13, 
    nombre: 'Consola PlayStation 5', 
    precio: 800, 
    stock: 2, 
    imagen: 'https://m.media-amazon.com/images/I/619BkvKW35L._AC_SL1500_.jpg',
    descripcion: 'Consola de última generación con gráficos 4K, SSD ultrarrápido y mando DualSense.'
  },
  { 
    id: 14, 
    nombre: 'Cámara Web Logitech C920', 
    precio: 75, 
    stock: 11, 
    imagen: 'https://m.media-amazon.com/images/I/71iNwni9TsL._AC_SL1500_.jpg',
    descripcion: 'Cámara web Full HD con micrófonos estéreo, ideal para videollamadas y streaming.'
  },
  { 
    id: 15, 
    nombre: 'Router WiFi TP-Link Archer', 
    precio: 60, 
    stock: 14, 
    imagen: 'https://th.bing.com/th/id/OIP.svCSNEaJF0Ukmm3rWvQ0GAHaHa?w=220&h=220&c=7&r=0&o=7&dpr=1.3&pid=1.7&rm=3',
    descripcion: 'Router de doble banda con tecnología MU-MIMO para mayor velocidad y cobertura.'
  },
])

const cart = ref([])



function handleAddToCart(id) {
  const product = products.value.find(p => p.id === id)
  if (product && product.stock > 0) {
    product.stock--          
    cart.value.push(product) 
  }
}

function handleLoginSuccess(user) {
  localStorage.setItem('currentUser', JSON.stringify(user))
  currentUser.value = user 
  isLoggedIn.value = true
}

const handleRegisterSuccess = () => {
  showRegister.value = false
  alert('Usuario registrado con éxito. Ahora podés loguearte.')
}

const logout = () => {
  localStorage.removeItem("currentUser"); // acá estaba 'user', debería ser 'currentUser'
  currentUser.value = null;
  isLoggedIn.value = false;
};


</script>

<style>


.boton span {

  top: 16px;

  z-index: 9999;
  background: linear-gradient(135deg, #6a11cb, #2575fc);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-weight: bold;
}

</style>