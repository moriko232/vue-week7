<template>
  <div>
    <div class="container">
      <div class="d-flex justify-content-between mb-2">
        <h2>訂單列表</h2>
      </div>
      <table class="table">
        <tr>
          <th>序號</th>
          <th>購買時間</th>
          <th>折價券</th>
          <th>金額</th>
          <th>購買明細</th>
          <th>付款狀態</th>
          <th>功能</th>
          <th>最後修改</th>
        </tr>
        <tr v-for="(orderData, index) in orderDatas" :key="orderData.id">
          <td>{{ (pagination.current_page - 1) * productListSettings.paged + index + 1 }}</td>
          <td>{{ orderData.created.datetime }} ( {{ orderData.created.diff }} )</td>
          <td>{{ orderData.coupon }}</td>
          <td>{{ orderData.amount }}</td>
          <!-- <td>{{ orderData.products }}</td> -->
          <td>
            <template v-for="(product, index) in orderData.products">
              <div :key="`product${index}`">
                {{ product.product.title }} ( ${{ product.product.price }} x
                {{ product.quantity }} )
              </div>
            </template>
          </td>
          <td>
            <template v-if="orderData.paid">
              <span class="text-success">已付款</span>
            </template>
            <template v-else>
              <span>未付款</span>
            </template>
          </td>
          <td>
            <button
              class="btn mr-2"
              :class="!orderData.paid ? 'btn-success' : 'btn-danger'"
              type="button"
              @click="setOrderPay(!orderData.paid, orderData.id)"
            >
              <template v-if="orderData.paid">
                <span>設為未付款</span>
              </template>
              <template v-else>
                <span>設為已付款</span>
              </template>
            </button>
          </td>
          <td>
            <div>{{ orderData.updated.datetime }}</div>
          </td>
        </tr>
      </table>

      <!-- pagination -->
      <Pagination :pages="pagination" @emit-pages="getData" name="pagination"></Pagination>
    </div>
    <div class="mt-5"></div>
  </div>
</template>
<script>
import Pagination from "@/components/_shared/admin/Pagination";

const getOrdersUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/admin/ec/orders`;
export default {
  components: {
    Pagination
  },
  data() {
    return {
      productListSettings: {
        page: 1,
        paged: 10
      },
      pagination: {
        current_page: 1
      },
      tempDelete: {
        id: "",
        title: ""
      },
      orderDatas: [],
      baseData: {},
      tempData: {}
    };
  },
  created() {
    this.token = document.cookie.replace(/(?:(?:^|.*;\s*)token\s*=\s*([^;]*).*$)|^.*$/, "$1");
    this.$http.defaults.headers["Authorization"] = `Bearer ${this.token}`;
    this.getData();
  },
  methods: {
    //取得資料 api
    getData(page = 1) {
      this.$parent.isLoading = true;
      const api = `${getOrdersUrl}`;
      this.productListSettings.page = page;
      this.$http.get(api, { params: this.productListSettings }).then(response => {
        this.orderDatas = response.data.data;
        this.pagination = response.data.meta.pagination;
        this.$parent.isLoading = false;
      });
    },

    //編輯資料api
    setOrderPay(setPay = true, id) {
      const payUrl = `${getOrdersUrl}/${id}/paid`;
      const unPayUrl = `${getOrdersUrl}/${id}/unpaid`;
      this.$parent.isLoading = true;

      this.$http.patch(setPay ? payUrl : unPayUrl, this.tempData).then(() => {
        this.getData();
      });
    }
  }
};
</script>
