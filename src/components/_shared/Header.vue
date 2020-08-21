<template>
  <header>
    <div class="container">
      <div class="d-flex align-items-center justify-content-between">
        <h1 class="logo">
          <router-link to="/">
            <h1>皂皂市集</h1>
          </router-link>
        </h1>
        <nav class="main-menu">
          <li>
            <router-link to="/admin/">後台管理</router-link>
          </li>
          <li>
            <router-link to="/products">產品列表</router-link>
          </li>
          <li>
            <router-link to="/about">關於皂皂</router-link>
          </li>
          <li>
            <router-link to="/cart">購物車</router-link>
          </li>
        </nav>
      </div>
    </div>

    <!-- loading 套件 start -->
    <loading :active.sync="isLoading"></loading>
    <!-- loading 套件 end -->
  </header>
</template>

<script>
export default {
  name: "Header",
  props: {
    isLoading: {
      type: Boolean,
      default: false
    }
  }
};
</script>
