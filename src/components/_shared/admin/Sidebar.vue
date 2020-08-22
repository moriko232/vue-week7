<template>
  <div>
    <div class="admin-title">皂皂市集<br />後台管理</div>
    <nav class="admin-menu">
      <li>
        <router-link to="products">產品管理</router-link>
      </li>
      <li>
        <router-link to="orders">訂單管理</router-link>
      </li>
      <li>
        <router-link to="coupon">折價券管理</router-link>
      </li>
      <li>
        <router-link to="storages">檔案管理</router-link>
      </li>
    </nav>

    <!-- loading 套件 start -->
    <loading :active.sync="isLoading"></loading>
    <!-- loading 套件 end -->
  </div>
</template>

<script>
export default {
  name: "AdminSidebar",
  props: {
    isLoading: {
      type: Boolean,
      default: false
    }
  }
};
</script>
