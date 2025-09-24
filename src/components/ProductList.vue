<template>
  <v-container>
    <v-row class="items-center mb-4">
      <v-col cols="12">
        <v-text-field
      v-model="search"
      label="Buscar producto"
      clearable
      class="mb-4"
>
  <template v-slot:append-inner>
    <v-badge
      :content="cart.length"
      color="red"
      offset-x="5"
      offset-y="5"
    >
  <div class="text-center">
    <v-btn
      :loading="loading"
      @click="loading = !loading ; cartOpen = true"
    >
      Mi Carrito🛒

      <template v-slot:loader>
        <v-progress-linear indeterminate></v-progress-linear>
      </template>
    </v-btn>
  </div>
  
   </v-badge>
  </template>
</v-text-field>
      </v-col>
    </v-row>

    <v-row>
      <template v-if="filteredProducts.length > 0">
        <v-col
          v-for="product in filteredProducts"
          :key="product.id"
          cols="12" sm="6" md="4"
        >
          <v-card class="mx-auto crecer" max-width="344">
            <v-img
              height="200px"
              :src="product.imagen"
              contain
            ></v-img>

            <v-card-title>
              {{ product.nombre }}
            </v-card-title>

            <v-card-subtitle>
              ${{ product.precio }}
            </v-card-subtitle>

            
            <v-card-actions>
              <v-btn
                color="orange-lighten-2"
                text="Detalles"
                @click="toggleExpand(product.id)"
              ></v-btn>

              <v-spacer></v-spacer>

              <v-btn
                :icon="expanded[product.id] ? 'mdi-chevron-up' : 'mdi-chevron-down'"
                @click="toggleExpand(product.id)"
              ></v-btn>
            </v-card-actions>

            <v-expand-transition>
              <div v-show="expanded[product.id]">
                <v-divider></v-divider>

                <v-card-text>
                  {{ product.descripcion }}
                  <v-card-actions>
                    <v-btn v-if="product.stock > 0" :disabled="product.stock === 0" outlined
                    color="primary">
                      Agregar al Carrito

                      <v-overlay
                        activator="parent"
                        location-strategy="connected"
                        scroll-strategy="close"
                        :disabled="product.stock === 0"
                        @click="addToCart(product)"
                      >
                        <v-card class="pa-2">
                          Producto Agregado!
                        </v-card>
                      </v-overlay>
                    </v-btn>
                    <span v-if="product.stock === 0" class="text-red">Agotado!</span>
                  </v-card-actions>
                </v-card-text>
              </div>
            </v-expand-transition>
          </v-card>
        </v-col>
      </template>

      <template v-else>
        <v-col cols="12">
          <v-alert type="warning" variant="tonal">
            No se encontraron productos.
          </v-alert>
        </v-col>
      </template>
    </v-row>

   
    <v-navigation-drawer
      v-model="cartOpen"
      location="right"
      temporary
    >
      <v-list>
        <v-list-item>
          <v-list-item-title class="text-h6">Carrito</v-list-item-title>
        </v-list-item>

        <v-divider></v-divider>

        <v-list-item
          v-for="item in cart"
          :key="item.id"
        >
          <v-list-item-title>
            {{ item.nombre }} (x{{ item.cantidad }}) - ${{ item.precio * item.cantidad }}
          </v-list-item-title>

            <div class="d-flex align-center">
                <v-btn   @click="decreaseQuantity(item)">
                  -
                </v-btn>

                <span class="mx-2">{{ item.cantidad }}</span>

                <v-btn   @click="increaseQuantity(item)">
                  +
                </v-btn>
              </div>


        </v-list-item>

        <v-divider></v-divider>

        <v-list-item>
          <v-list-item-title class="font-bold">
            Total: ${{ total }}
          </v-list-item-title>
        </v-list-item>
        <v-divider></v-divider>

        <div class="d-flex justify-end pa-4">
          <v-btn
            color="red"
            variant="tonal"
            @click="clearCart"
          >
            Vaciar carrito
          </v-btn>
        </div>
      </v-list>
    </v-navigation-drawer>
  </v-container>
</template>

<script setup>
import { ref, reactive, computed, watch } from 'vue'

const loading = ref(false)
watch(loading, val => {
  if (!val) return
  setTimeout(() => (loading.value = false), 2000)
})

const increaseQuantity = (product) => {
  if (product.stock <= 0) return; // No más stock disponible

  const existing = cart.value.find(p => p.id === product.id)
  if (existing) {
    existing.cantidad++
    product.stock--
  }
}

const decreaseQuantity = (product) => {
  const existing = cart.value.find(p => p.id === product.id)
  if (!existing) return

  existing.cantidad--

  // Buscar el producto original y aumentar su stock
  const prodOriginal = props.products.find(p => p.id === product.id)
  if (prodOriginal) prodOriginal.stock++

  if (existing.cantidad <= 0) {
    const index = cart.value.findIndex(p => p.id === product.id)
    if (index !== -1) cart.value.splice(index, 1)
  }
}

const props = defineProps({
  products: { type: Array, required: true }
})

const search = ref('')
const expanded = reactive({})
const cart = ref([])
const cartOpen = ref(false)

const filteredProducts = computed(() => {
  if (!search.value) return props.products
  return props.products.filter(p =>
    p.nombre.toLowerCase().includes(search.value.toLowerCase())
  )
})

const toggleExpand = (id) => {
  expanded[id] = !expanded[id]
}

const clearCart = (product) => {
  cart.value.forEach(item => {
    const prod = props.products.find(p => p.id === item.id)
    if (prod) prod.stock += item.cantidad
  })
  cart.value = []
}

const addToCart = (product) => {
  const existing = cart.value.find(p => p.id === product.id)
  if (existing) {
    existing.cantidad++
    
  } else {
    cart.value.push({ ...product, cantidad: 1 })
  }
  product.stock--
}



const total = computed(() =>
  cart.value.reduce((sum, item) => sum + item.precio * item.cantidad, 0)
)
</script>

<style scoped>
.text-red {
  color: red;
}
.text-green {
  color: green;
}

.crecer{
  transition: transform 0.2s ease-in-out;
}

.crecer:hover{
  transform: scale(1.1);
}
</style>
