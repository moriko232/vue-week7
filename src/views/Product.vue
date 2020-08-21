<template>
  <div class="product">
    <Header :isLoading="isLoading"></Header>
    <div class="main-content" v-if="!isLoading">
      <div class="container">
        <router-link to="/products">back</router-link>
        <div class="row align-items-start justify-content-center">
          <div class="product-item col-md-6">
            <h1 class="title">{{ item.title }}</h1>
            <img :src="item.imageUrl[0]" />
            <div class="product-text">
              <p class="text">{{ item.content }}</p>
              <div class="text-right">
                <p class="price">$ {{ item.price }}</p>
                <p class="origin-price">原價 $ {{ item.origin_price }}</p>
              </div>
            </div>

            <span>
              <button class="add-cart" @click="addToCart(item, 1)">加入購物車</button>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Header from "@/components/_shared/Header.vue";
const getProductDetailUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/ec/product`;

export default {
  name: "Product",
  data() {
    return {
      isLoading: false,
      item: {
        id: "",
        title: "",
        content: "",
        price: "",
        origin_price: "",
        imageUrl: []
      }
    };
  },
  components: {
    Header
  },
  created() {
    this.getProductDetailApi();
  },
  methods: {
    getProductDetailApi() {
      this.isLoading = true;
      this.$http
        .get(`${getProductDetailUrl}/${this.$route.params.id}`)
        .then(res => {
          this.item = res.data.data;
          this.isLoading = false;
        })
        .catch(function(err) {
          console.log("err", err);
        });
    }
  }
};
</script>
