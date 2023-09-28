<template>
  <div
    :class="{
      'bg colman': product.category === 'men\'s clothing',
      'bg colwoman': product.category === 'women\'s clothing',
      bgkosong: product.category !== 'men\'s clothing' && product.category !== 'women\'s clothing',
    }"
  >
    <div class="container-blank" v-if="!isCategoryValid(product.category)">
      <div class="loader" v-if="isLoading"></div>
      <div class="else-blank" v-else>
        <img src="../assets/img/sad-face.png" alt="" />
        <p>This product is unavailable to show</p>
        <button @click="loadNextProduct">Next Product</button>
      </div>
    </div>
    <div class="container" v-else>
      <div class="loader" v-if="isLoading"></div>
      <div class="else" v-else>
        <div class="image-product">
          <img :src="product.image" alt="" />
        </div>
        <div class="info-product">
          <div class="title-product" :class="{ 'txt-colman': product.category === 'men\'s clothing', 'txt-colwoman': product.category === 'women\'s clothing' }">
            <h1>{{ product.title }}</h1>
          </div>
          <div class="category-rating">
            <p>{{ product.category }}</p>
            <div class="rating">
              <p v-if="product.rating">{{ product.rating.rate }}</p>
              <div class="stars" v-if="product.rating">
                <!-- Loop untuk membuat bintang yang diisi -->
                <div
                  v-for="(item, index) in Math.round(product.rating.rate)"
                  :key="'filled-' + index"
                  class="star-filled"
                  :class="{ 'star-filled-man': product.category === 'men\'s clothing', 'star-filled-women': product.category === 'women\'s clothing' }"
                ></div>

                <!-- Loop untuk membuat bintang yang kosong -->
                <div
                  v-for="(item, index) in 5 - Math.round(product.rating.rate)"
                  :key="'empty-' + index"
                  class="star-empty"
                  :class="{ 'border-star-empty-man': product.category === 'men\'s clothing', 'border-star-empty-women': product.category === 'women\'s clothing' }"
                ></div>
              </div>
            </div>
          </div>
          <div class="description">
            <p>
              {{ product.description }}
            </p>
          </div>
          <div class="price" :class="{ 'txt-colman': product.category === 'men\'s clothing', 'txt-colwoman': product.category === 'women\'s clothing' }">
            <h1>${{ product.price }}</h1>
          </div>
          <div class="btn">
            <button :class="{ 'bg-color-man border-man': product.category === 'men\'s clothing', 'bg-color-women border-women': product.category === 'women\'s clothing' }">Buy Now</button>
            <button class="txt-colman border-man" :class="{ 'txt-colman border-man': product.category === 'men\'s clothing', 'txt-colwoman border-women': product.category === 'women\'s clothing' }" @click="loadNextProduct">
              Next Product
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductDisplay',
  data() {
    return {
      product: {}, //// Objek untuk menyimpan data produk dari API
      currentProductId: 1, // ID produk saat ini
      totalProducts: 0, // Jumlah total produk dari API
      isLoading: false, // Status loading
    };
  },
  methods: {
    async fetchTotalProducts() {
      try {
        const response = await fetch('https://fakestoreapi.com/products');
        const json = await response.json();
        console.log(json);
        this.totalProducts = json.length; // Mengambil panjang array dari hasil respons
      } catch (error) {
        console.error('Terjadi kesalahan:', error);
      }
    },
    async loadProduct() {
      this.isLoading = true; // Menampilkan loader saat permintaan API dimulai
      try {
        const response = await fetch(`https://fakestoreapi.com/products/${this.currentProductId}`);
        const json = await response.json();
        this.product = json;
      } catch (error) {
        console.error('Terjadi kesalahan:', error);
      } finally {
        this.isLoading = false; // Menyembunyikan loader saat permintaan API selesai
      }
    },

    isCategoryValid(category) {
      // Fungsi ini memeriksa apakah kategori produk valid
      return category === "men's clothing" || category === "women's clothing";
    },
    loadNextProduct() {
      console.log('loadNextProduct dijalankan');
      // Menambah ID produk saat ini untuk mengambil produk berikutnya
      this.currentProductId++;
      // Memastikan bahwa ID produk saat ini tidak melebihi jumlah total produk
      if (this.currentProductId <= this.totalProducts) {
        this.loadProduct();
      } else {
        this.currentProductId = 0;
      }
    },
  },
  async created() {
    await this.fetchTotalProducts(); // Memanggil metode untuk mengambil total produk saat komponen dibuat
    await this.loadProduct();
  },
};
</script>
