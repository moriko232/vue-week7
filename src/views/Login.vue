<template>
  <div class="full-bg">
    <div class="top">
      <h3>皂皂市集管理後台</h3>
    </div>
    <div class="container">
      <div class="row">
        <div class=" col-4 justify-content-center m-auto white">
          <h1 class="text-center mb-3 font-weight-bold">後台登入</h1>
          <form @submit.prevent="login">
            <div class="mb-2">
              <input
                type="email"
                class="form-control"
                v-model="user.email"
                placeholder="帳號(Email)"
                required
                autofocus
                @input="clearMessage"
              />
            </div>
            <div class="mb-2">
              <input
                type="password"
                class="form-control"
                v-model="user.password"
                placeholder="密碼"
                @input="clearMessage"
                required
              />
            </div>
            <button class="btn btn-dark form-control" type="submit">登入</button>
          </form>

          <div v-if="errorMessage" class="alert alert-danger" role="alert">
            {{ errorMessage }}
          </div>

          <div v-if="successMessage" class="alert alert-success" role="alert">
            {{ successMessage }}
          </div>
        </div>
      </div>
    </div>
    <div class="mt-5"></div>
  </div>
</template>
<script>
const loginUrl = `${process.env.VUE_APP_PATH}auth/login`;

export default {
  data() {
    return {
      user: {
        email: "",
        password: ""
      },
      errorMessage: "",
      successMessage: ""
    };
  },
  methods: {
    login() {
      const data = {
        email: this.user.email,
        password: this.user.password
      };

      this.$http
        .post(loginUrl, data)
        .then(response => {
          this.successMessage = "登入成功，請等待跳轉";

          const token = response.data.token;
          const expired = response.data.expired;
          console.log("rea", response);
          document.cookie = `token=${token}`;
          document.cookie = `expired=${new Date(expired * 1000)}`;
          this.$router.push({ path: "/admin/products" });
        })
        .catch(err => {
          if (err) {
            this.errorMessage = "登入失敗";
            this.password = "";
          }
          console.log("err", err);
        });
    },
    clearMessage() {
      this.errorMessage = "";
      this.successMessage = "";
    }
  }
};
</script>
<style>
.full-bg {
  height: 100vh;
  background-color: #a0d6aa;
  display: flex;
  align-items: center;
  position: relative;
}
.white-bg .top,
.full-bg .top {
  position: absolute;
  top: 0;
  width: 100%;
  min-height: 60px;
  padding: 15px 30px;
  color: #ffffff;
  background: #90bd98;
  font-size: 18px;
}
.white-bg {
  background-color: #fff;
  min-height: 100vh;
  align-items: center;
  position: relative;
  padding-top: 100px;
}
</style>
