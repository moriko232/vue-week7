<template>
  <div class="products">
    <Header :isLoading="isLoading"></Header>
    <nav class="sub-menu-wrap">
      <div class="container">
        <ul class="sub-menu">
          <li>
            <a href="#">手工皂</a>
          </li>
          <li>
            <a href="#">優惠組合</a>
          </li>
        </ul>
      </div>
    </nav>

    <div class="main-content">
      <div class="container">
        <template>
          <div class="row justify-content-between">
            <div class="col-6">
              <h2>購物清單</h2>
            </div>
            <div v-if="shopCart.length" class="col-6 text-right">
              <button class="btn btn-danger" @click="deleteCartAll()">清空購物車</button>
            </div>
          </div>
          <div class="row">
            <div class="col-12">
              <table class="table mb-3">
                <tr>
                  <th>品名</th>
                  <th>數量</th>
                  <th>價格</th>
                  <th class="text-right">小計</th>
                  <th></th>
                </tr>
                <template v-for="(item, index) in shopCart">
                  <tr :key="`cart${index}`">
                    <td>{{ item.product.title }}</td>
                    <td>
                      <div class="qty">
                        <input
                          class="form-control count"
                          type="number"
                          name="qty"
                          :value="item.quantity"
                          step="1"
                          @input="editCart(item.product.id, $event.target.value)"
                        />
                      </div>
                    </td>
                    <td>{{ item.product.price }}</td>
                    <td class="text-right">{{ item.product.price * item.quantity }}</td>
                    <td class="text-right">
                      <button class="btn btn-danger" @click="deleteCartItem(item.product.id)">
                        刪除
                      </button>
                    </td>
                  </tr>
                </template>

                <template v-if="shopCart.length">
                  <tr>
                    <td>總計</td>
                    <td></td>
                    <td></td>
                    <td class="text-right">{{ total }}</td>
                    <td></td>
                  </tr>
                </template>
                <template v-else>
                  <tr>
                    <td colspan="5" class="text-center">購物尚無商品</td>
                  </tr>
                </template>
              </table>

              <div>
                <h2>填寫資料</h2>
              </div>
              <validation-observer v-slot="{ invalid, handleSubmit }">
                <form action="" class="form" @submit.prevent="handleSubmit(sendOrderApi)">
                  <validation-provider rules="required" v-slot="{ errors, classes }">
                    <h3>收件人姓名</h3>
                    <input
                      name="收件人姓名"
                      v-model="customer.name"
                      class="form-control"
                      :class="classes"
                    />
                    <span :class="classes">{{ errors[0] }}</span>
                  </validation-provider>
                  <validation-provider rules="required|email" v-slot="{ errors, classes }">
                    <h3>Email</h3>
                    <input
                      type="email"
                      name="email"
                      v-model="customer.email"
                      class="form-control"
                      :class="classes"
                    />
                    <span :class="classes">{{ errors[0] }}</span>
                  </validation-provider>
                  <validation-provider rules="required|min:8" v-slot="{ errors, classes }">
                    <h3>電話</h3>
                    <input
                      name="電話"
                      v-model="customer.tel"
                      class="form-control"
                      :class="classes"
                    />
                    <span :class="classes">{{ errors[0] }}</span>
                  </validation-provider>
                  <validation-provider rules="required" v-slot="{ errors, classes }">
                    <h3>地址</h3>
                    <input
                      name="地址"
                      v-model="customer.address"
                      class="form-control"
                      :class="classes"
                    />
                    <span :class="classes">{{ errors[0] }}</span>
                  </validation-provider>

                  <div class="form-item">
                    <h3>付款方式</h3>
                    <div class="form-group">
                      <select class="form-control" v-model="customer.payment">
                        <option value="WebATM">WebATM</option>
                        <option value="ATM">ATM</option>
                        <option value="CVS">CVS</option>
                        <option value="Barcode">Barcode</option>
                        <option value="Credit">Credit</option>
                        <option value="ApplePay">ApplePay</option>
                        <option value="GooglePay">GooglePay</option>
                      </select>
                    </div>
                  </div>
                  <div class="form-item">
                    <h3>留言</h3>
                    <textarea
                      name=""
                      id=""
                      cols="30"
                      rows="10"
                      class="form-control"
                      v-model="customer.message"
                    ></textarea>
                  </div>
                  <br />

                  <input
                    class="btn btn-info"
                    :disabled="invalid || !shopCart.length || isLoading"
                    type="submit"
                    value="確認訂購"
                  />
                </form>
              </validation-observer>
            </div>
          </div>
        </template>
        <!-- 產品列表 -->
        <h2 id="products">手工皂</h2>
        <div id="product-list" class="row">
          <template v-for="(item, index) in products">
            <div class="product-item col-md-4" :key="`item${index}`">
              <img :src="item.imageUrl[0]" />
              <div class="product-text">
                <h5 class="title">
                  <router-link :to="`/product/${item.id}`">{{ item.title }}</router-link>
                </h5>
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
          </template>
        </div>
      </div>

      <!-- Modal -->
      <div
        class="modal fade bd-example-modal-lg"
        id="messageModal"
        tabindex="-1"
        role="dialog"
        aria-labelledby="exampleModalCenterTitle"
        aria-hidden="true"
      >
        <div class="modal-dialog modal-sm modal-dialog-centered" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLongTitle">
                系統訊息
              </h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              {{ messageText }}
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-primary" @click="closeMessage()">OK</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "@/components/_shared/Header.vue";
/* global $*/
const getProductUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/ec/products`;
const editShopCartUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/ec/shopping`;
const editShopCartAllUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/ec/shopping/all/product`;
const sendOrderUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/ec/orders`;

export default {
  name: "Products",
  components: {
    Header
  },
  data() {
    return {
      isLoading: true,
      products: [],
      shopCart: [],
      customer: {
        name: "",
        email: "",
        tel: "",
        address: "",
        payment: "WebATM",
        message: ""
      },
      messageModelOpen: false,
      messageText: ""
    };
  },
  computed: {
    total() {
      let sum = 0;
      this.shopCart.forEach(item => {
        sum += item.product.price * item.quantity;
      });
      return sum;
    }
  },
  mounted() {
    this.getDataApi();
    this.getShopCartApi();
    console.log();
  },
  methods: {
    openMessage(msg) {
      this.messageText = msg;
      $("#messageModal").modal("show");
      this.messageModelOpen = true;
    },
    closeMessage() {
      this.messageText = "";
      $("#messageModal").modal("hide");
      this.messageModelOpen = false;
    },
    sendOrderApi() {
      this.isLoading = true;
      this.$http
        .post(sendOrderUrl, this.customer)
        .then(() => {
          this.getShopCartApi();
          this.shopCart = [];
          this.isLoading = false;
          this.openMessage("已收到您的訂單!");
        })
        .catch(function(err) {
          console.log("err", err);
        });
    },
    getShopCartApi() {
      this.$http
        .get(editShopCartUrl)
        .then(res => {
          this.shopCart = res.data.data;
        })
        .catch(function(err) {
          console.log("err", err);
        });
    },
    isAlreadyInCart(id) {
      return this.shopCart.findIndex(item => {
        return item.id === id;
      });
    },
    deleteCartAll() {
      this.isLoading = true;
      this.$http.delete(editShopCartAllUrl).then(res => {
        this.shopCart = res.data.data;
        this.getDataApi();
        this.getShopCartApi();
        this.isLoading = false;
      });
    },
    deleteCartItem(id) {
      this.isLoading = true;
      this.$http.delete(`${editShopCartUrl}/${id}`).then(res => {
        this.shopCart = res.data.data;
        this.getDataApi();
        this.getShopCartApi();
        this.isLoading = false;
      });
    },
    editCart(id, quantity = 1) {
      this.isLoading = true;
      const data = {
        product: id,
        quantity: quantity
      };
      this.$http
        .patch(editShopCartUrl, data)
        .then(res => {
          this.shopCart = res.data.data;

          this.getDataApi();
          this.getShopCartApi();
          this.isLoading = false;
        })
        .catch(function(err) {
          console.log("err", err);
        });
    },
    addToCart(item, quantity = 1) {
      this.isLoading = true;
      const data = {
        product: item.id,
        quantity: quantity
      };
      this.$http
        .post(editShopCartUrl, data)
        .then(res => {
          this.shopCart = res.data.data;

          this.getDataApi();
          this.getShopCartApi();
          this.isLoading = false;
        })
        .catch(function(err) {
          console.log("err", err);
        });
    },
    getDataApi() {
      this.isLoading = true;
      this.$http
        .get(getProductUrl)
        .then(res => {
          this.products = res.data.data;
          this.isLoading = false;
        })
        .catch(function(err) {
          console.log("err", err);
        });
    }
  }
};
</script>
