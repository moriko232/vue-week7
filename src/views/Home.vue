<template>
  <div class="home">
    <!-- loading 套件 start -->
    <loading :active.sync="isLoading"></loading>
    <!-- loading 套件 end -->
    <Header></Header>

    <div class="main-content">
      <div class="container">
        <div class="row align-items-start justify-content-center">
          <div class="col-md-12">
            <img
              src="https://images.unsplash.com/photo-1548110579-405ee7448814?ixlib=rb-1.2.1&auto=format&fit=crop&w=1351&q=80"
              alt=""
            />
            <div class="mt-3">
              <p class="text-center">
                秋季手工皂開放預訂!
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "@/components/_shared/Header.vue";

export default {
  name: "Home",
  components: {
    Header
  },
  data() {
    return {
      isLoading: false
    };
  },
  computed: {},
  mounted() {},
  methods: {}
};
</script>
