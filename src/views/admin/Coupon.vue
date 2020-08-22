<template>
  <div>
    <div class="container">
      <div class="d-flex justify-content-between mb-2">
        <h2>折價券列表</h2>
        <button class="btn btn-primary" type="button" @click="editData()">新增折價券</button>
      </div>
      <table class="table">
        <tr>
          <th>序號</th>
          <th>名稱</th>
          <th>折扣百分比</th>
          <th>是否啟用</th>
          <th>到期日</th>
          <th>功能</th>
        </tr>
        <tr v-for="(couponData, index) in couponDatas" :key="`coupon${index}`">
          <td>{{ couponData.code }}</td>
          <td>{{ couponData.title }}</td>
          <td>{{ couponData.percent }}</td>
          <td>
            <template v-if="couponData.enabled">
              <span class="text-success">是</span>
            </template>
            <template v-else>
              <span>否</span>
            </template>
          </td>
          <td>
            {{ couponData.deadline.datetime }}<br />
            ({{ couponData.deadline.diff }})
          </td>
          <td>
            <button class="btn btn-primary mr-2" type="button" @click="editData(couponData)">
              修改
            </button>
            <button
              class="btn btn-danger"
              type="button"
              @click="deleteQA(couponData.id, couponData.title)"
            >
              刪除
            </button>
          </td>
        </tr>
      </table>

      <!-- pagination -->
      <Pagination :pages="pagination" @emit-pages="getData" name="pagination"></Pagination>

      <!-- deleteModal -->
      <div
        class="modal fade bd-example-modal-sm"
        id="deleteModal"
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
            <div class="modal-body">確定要刪除 {{ tempDelete.title }}?</div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>

              <button type="button" class="btn btn-danger" @click="deleteData()">確認刪除</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Modal -->
      <div
        class="modal fade bd-example-modal-lg"
        id="editModal"
        tabindex="-1"
        role="dialog"
        aria-labelledby="exampleModalCenterTitle"
        aria-hidden="true"
      >
        <div class="modal-dialog modal-lg modal-dialog-centered" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLongTitle">
                <template v-if="tempData.id">
                  編輯折價券
                </template>
                <template v-else>
                  新增折價券
                </template>
              </h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <form class="form-group">
                <div class="row">
                  <div class="col-sm-12">
                    <table>
                      <tr>
                        <th width="30%">
                          折價券標題
                        </th>
                        <td width="70%">
                          <input
                            type="text"
                            class="form-control"
                            v-model="tempData.title"
                            placeholder="折價券標題"
                            required
                          />
                        </td>
                      </tr>
                      <tr>
                        <th>序號</th>
                        <td>
                          <input
                            type="text"
                            class="form-control"
                            v-model.number="tempData.code"
                            placeholder="序號"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th>折扣百分比</th>
                        <td>
                          <input
                            type="text"
                            class="form-control"
                            v-model.number="tempData.percent"
                            placeholder="折扣百分比"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th>是否啟用</th>
                        <td>
                          <div class="custom-control custom-switch">
                            <input
                              type="checkbox"
                              class="custom-control-input"
                              id="customSwitches"
                              v-model="tempData.enabled"
                            />
                            <label class="custom-control-label" for="customSwitches"></label>
                          </div>
                        </td>
                      </tr>
                      <tr>
                        <th>到期日</th>
                        <td>
                          <div class="custom-control custom-switch">
                            <input
                              type="text"
                              class="form-control"
                              v-model="dueDate"
                              placeholder="Y-m-d"
                            />

                            <input
                              type="text"
                              class="form-control"
                              v-model="dueTime"
                              placeholder="H:i:s"
                            />
                          </div>
                        </td>
                      </tr>
                    </table>
                  </div>
                </div>
              </form>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>

              <button type="button" class="btn btn-primary" @click="updateData()">送出</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="mt-5"></div>
  </div>
</template>
<script>
/* global $*/
import Pagination from "@/components/_shared/admin/Pagination";
import moment from "moment";

const getCouponUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/admin/ec/coupons`;
const editCouponUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/admin/ec/coupon`;
export default {
  components: {
    Pagination
  },
  data() {
    return {
      coupontListSettings: {
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
      couponDatas: [],
      baseData: {
        deadline: [],
        title: "折價券",
        enabled: false,
        percent: 100,
        deadline_at: 0,
        code: ""
      },
      dueDate: "",
      dueTime: "",
      tempData: {
        title: "",
        enabled: false,
        percent: 100,
        deadline_at: 0,
        code: ""
      }
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
      const api = `${getCouponUrl}`;
      this.coupontListSettings.page = page;
      this.$http.get(api).then(response => {
        this.couponDatas = response.data.data;
        this.$parent.isLoading = false;
        this.pagination = response.data.meta.pagination;
      });
    },
    //新增+編輯資料
    updateData() {
      // 日期格式 Y-m-d H:i:s，例如：「2020-06-16 09:31:18」
      this.tempData.deadline_at = `${this.dueDate} ${this.dueTime}`;

      //update data
      if (this.tempData.id) {
        this.apiEditData(this.tempData.id);
        this.couponDatas.forEach((couponData, idx) => {
          if (this.tempData.id === couponData.id) {
            this.couponDatas[idx] = this.tempData;
          }
        });
      } else {
        //new data
        const id = new Date().getTime(); // Unix Timestamp
        this.tempData.id = id;
        this.couponDatas.push(this.tempData);
        this.apiAddNewData(this.tempData);
      }
      //折價券表單回復空值
      this.tempData = JSON.parse(JSON.stringify(this.baseData));
      $("#editModal").modal("hide");
    },

    //編輯資料
    editData(data = this.baseData) {
      this.tempData = JSON.parse(JSON.stringify(data));

      // 日期
      if (this.tempData.deadline.length) {
        const dueDateTime = this.tempData.deadline.datetime.split(" ");
        [this.dueDate, this.dueTime] = dueDateTime;
      } else {
        this.dueDate = moment().format("yyyy-MM-DD");
        this.dueTime = moment().format("hh:mm:ss");
      }
      $("#editModal").modal("show");
    },
    //編輯資料api
    apiEditData(id) {
      const url = `${editCouponUrl}/${id}`;
      this.$parent.isLoading = true;
      this.$http.patch(url, this.tempData).then(() => {
        this.getData();
      });
    },
    //新增資料api
    apiAddNewData(data) {
      this.$parent.isLoading = true;
      this.$http.post(editCouponUrl, data).then(() => {
        this.getData();
      });
    },

    deleteQA(id, title) {
      this.tempDelete.id = id;
      this.tempDelete.title = title;
      $("#deleteModal").modal("show");
    },
    deleteData() {
      const id = this.tempDelete.id;
      this.apiDeleteData(id);
      //update screen
      this.couponDatas.forEach((couponData, idx) => {
        if (couponData.id === id) {
          this.couponDatas.splice(idx, 1);
        }
      });
      this.tempDelete.id = "";
      this.tempDelete.title = "";
      $("#deleteModal").modal("hide");
    },
    apiDeleteData(id) {
      this.$parent.isLoading = true;
      //api
      const url = `${editCouponUrl}/${id}`;
      this.$http.delete(url).then(() => {
        this.getData();
      });
    }
  }
};
</script>
