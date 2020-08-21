<template>
  <div class="admin-main">
    <!-- loading 套件 start -->
    <loading :active.sync="isLoading"></loading>
    <!-- loading 套件 end -->
    <div class="admin-main__left">
      <Sidebar></Sidebar>

      <div class="mt-auto">
        <nav class="admin-menu">
          <li>
            <a href="#" @click.prevent="logoutQA">登出</a>
          </li>
        </nav>
      </div>
    </div>
    <div class="admin-main__right">
      <router-view></router-view>

      <!-- deleteModal -->
      <div
        class="modal fade bd-example-modal-sm"
        id="logoutModal"
        tabindex="-1"
        role="dialog"
        aria-labelledby="exampleModalCenterTitle"
        aria-hidden="true"
      >
        <div class="modal-dialog modal-sm modal-dialog-centered" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLongTitle">確認操作</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">確定要登出嗎 ?</div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>

              <button type="button" class="btn btn-danger" @click="logOut()">登出</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
/* global $*/
import Sidebar from "@/components/_shared/admin/Sidebar";
export default {
  name: "Dashboard",
  components: { Sidebar },
  data() {
    return {
      isLoading: false
    };
  },
  created() {
    this.token = document.cookie.replace(/(?:(?:^|.*;\s*)token\s*=\s*([^;]*).*$)|^.*$/, "$1");
    this.$http.defaults.headers["Authorization"] = `Bearer ${this.token}`;
    //token不存在則導回登入畫面
    if (this.token === "") {
      // redirect to login page
      this.$router.push({ path: "/login" });
    }
  },
  methods: {
    //登出
    logOut() {
      $("#logoutModal").modal("hide");
      this.deleteCookie("token");
      this.deleteCookie("expired");
      this.$router.push({ path: "/login" });
    },
    deleteCookie(name, path, domain) {
      if (this.getCookie(name)) {
        document.cookie =
          name +
          "=" +
          (path ? ";path=" + path : "") +
          (domain ? ";domain=" + domain : "") +
          ";expires=Thu, 01 Jan 1970 00:00:01 GMT";
      }
    },

    getCookie(name) {
      return document.cookie.split(";").some(c => {
        return c.trim().startsWith(name + "=");
      });
    },
    logoutQA() {
      $("#logoutModal").modal("show");
    }
  }
};
</script>
