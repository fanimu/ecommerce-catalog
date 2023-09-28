<template>
  <div>
    <ProductDisplay />
  </div>
</template>

<script>
import './assets/style/style.css';
import ProductDisplay from './components/ProductDisplay.vue';

export default {
  name: 'App',
  components: {
    ProductDisplay,
  },
};
</script>
