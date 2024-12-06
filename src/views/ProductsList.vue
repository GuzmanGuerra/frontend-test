<!-- eslint-disable eqeqeq -->
<template>
  <div class="product-list">
    <h1>Product List</h1>
    <div v-if="loading" class="loading">Loading...</div>
    <div v-if="error" class="error">{{ error }}</div>
    
    <div v-if="!loading && !error" class="products">
      <div v-for="product in products" :key="product.id">
        <!--Modificamos el nombre de del evento en escucha para capturarlo correctamente -->
        <product-card 
          :product="product"
          @productFavoriteClicked="toggleProductFavorite"
        />
      </div>
    </div>
  </div>
</template> 

<script>
import axios from 'axios'
import ProductCard from '../components/ProductCard.vue'
import { getProductsListEndpoint } from '@/helpers/constants';

export default {
  name: 'ProductsList',
  components: {
    ProductCard
  },
  created () {
    this.fetchProducts()
  },
  //Se elimina el hook mounted ya que duplica la peticion de los productos
  //Mantenemos la peticion en el hook created ya que se ejecuta primero en el lifecycle permitiendo optimizar los tiempos de carga mientras se carga el DOM
  data () {
    return {
      products: [], 
      loading: true,
      error: null
    }
  },
  methods: {
    async fetchProducts () {
      try {
        const response = await axios.get(getProductsListEndpoint) 
        this.products = response.data.slice(0, 5)
      } catch (err) {
        this.error = 'Failed to load products'
      } finally {
        this.loading = false
      }
    },
    // Simplificamos la funcion handler para modificar el estado del producto seleccionado
    // permitiendo invertir el flag de favorite si es necesario
    // También se modifican los parametros de entrada de la función aceptando solo el Id y el flag de Favorito  
    toggleProductFavorite(productSelectedId, productFavoriteFlag) {      
      this.products = this.products.map(product => { 
        if (product.id === productSelectedId) {
          product.favorite = productFavoriteFlag
        }
        return product
      })
      // Devolvemos una copia de la variable products
      return [...this.products]
    }
  }
}
</script>

<style scoped>
.product-list {
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.loading {
  font-size: 18px;
  color: gray;
}

.error {
  color: red;
  font-size: 18px;
}

.products {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}
</style>
