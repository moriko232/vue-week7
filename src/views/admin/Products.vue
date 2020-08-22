<template>
  <div>
    <div class="container">
      <div class="d-flex justify-content-between mb-2">
        <h2>產品列表</h2>
        <button class="btn btn-primary" type="button" @click="editData()">新增產品</button>
      </div>
      <table class="table">
        <tr>
          <th>序號</th>
          <th>產品名稱</th>
          <th>商品上架</th>
          <th>商品分類</th>
          <th>原價</th>
          <th>售價</th>
          <th>功能</th>
        </tr>
        <tr v-for="(shopData, index) in shopDatas" :key="shopData.id">
          <td>{{ (pagination.current_page - 1) * productListSettings.paged + index + 1 }}</td>
          <td>{{ shopData.title }}</td>
          <td>
            <template v-if="shopData.enabled">
              <span class="text-success">是</span>
            </template>
            <template v-else>
              <span>否</span>
            </template>
          </td>
          <td>{{ shopData.category }}</td>
          <td>{{ shopData.origin_price }}</td>
          <td>{{ shopData.price }}</td>
          <td>
            <button class="btn btn-primary mr-2" type="button" @click="editData(shopData)">
              修改
            </button>
            <button
              class="btn btn-danger"
              type="button"
              @click="deleteQA(shopData.id, shopData.title)"
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
                  編輯產品
                </template>
                <template v-else>
                  新增產品
                </template>
              </h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <form class="form-group w-100">
                <div class="row">
                  <div class="col-sm-6">
                    <table class="w-100">
                      <tr>
                        <th width="30%">
                          產品標題
                        </th>
                        <td width="70%">
                          <input
                            type="text"
                            class="form-control"
                            v-model="tempData.title"
                            placeholder="產品標題"
                            required
                          />
                        </td>
                      </tr>
                      <tr>
                        <th>分類</th>
                        <td>
                          <input
                            type="text"
                            class="form-control"
                            v-model="tempData.category"
                            placeholder="分類"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th>內容</th>
                        <td>
                          <textarea
                            type="text"
                            class="form-control"
                            v-model="tempData.content"
                            placeholder="內容"
                          ></textarea>
                        </td>
                      </tr>
                      <tr>
                        <th>原價</th>
                        <td>
                          <input
                            type="text"
                            class="form-control"
                            v-model.number="tempData.origin_price"
                            placeholder="原價"
                          />
                        </td>
                      </tr>
                      <tr>
                        <th>售價</th>
                        <td>
                          <input
                            type="text"
                            class="form-control"
                            v-model.number="tempData.price"
                            placeholder="售價"
                          />
                        </td>
                      </tr>
                    </table>
                  </div>

                  <div class="col-sm-6">
                    <table class="w-100">
                      <tr>
                        <th width="30%">單位</th>
                        <td width="70%">
                          <input
                            type="text"
                            class="form-control"
                            v-model="tempData.unit"
                            placeholder="單位"
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
                        <th>敘述</th>
                        <td>
                          <input
                            type="text"
                            class="form-control"
                            v-model="tempData.description"
                            placeholder="敘述"
                          />
                        </td>
                      </tr>
                    </table>

                    <hr />
                    <div class="mb-3"><b>圖片網址</b></div>

                    <div v-for="imgIdx in 3" class="mt-3" :key="imgIdx">
                      <input
                        type="text"
                        class="form-control"
                        v-model="tempData.imageUrl[imgIdx - 1]"
                        placeholder="圖片網址"
                      />
                      <img
                        v-if="tempData.imageUrl[imgIdx - 1]"
                        :src="tempData.imageUrl[imgIdx - 1]"
                        alt=""
                      />
                    </div>
                    <div class="mt-3">
                      <table class="w-100">
                        <tr>
                          <th width="30%">
                            <label for="customFile">
                              上傳圖片
                            </label>
                          </th>
                          <td width="70%">
                            <input
                              id="customFile"
                              ref="file"
                              type="file"
                              class="form-control"
                              @change="uploadFile"
                            />
                          </td>
                        </tr>
                      </table>
                    </div>
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

const baseUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}`;
const getProductUrl = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/admin/ec/products`;
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
      shopDatas: [],
      baseData: {
        imageUrl: []
      },
      tempData: {
        imageUrl: []
      }
    };
  },
  created() {
    this.getData();
  },
  methods: {
    //取得資料 api
    getData(page = 1) {
      this.$parent.isLoading = true;
      const api = `${getProductUrl}`;
      this.productListSettings.page = page;
      this.$http.get(api, { params: this.productListSettings }).then(response => {
        this.shopDatas = response.data.data;
        this.pagination = response.data.meta.pagination;
        this.$parent.isLoading = false;
      });
    },
    //新增+編輯資料
    updateData() {
      //update data
      if (this.tempData.id) {
        this.apiEditData(this.tempData.id);
        this.shopDatas.forEach((shopData, idx) => {
          if (this.tempData.id === shopData.id) {
            this.shopDatas[idx] = this.tempData;
          }
        });
      } else {
        //new data
        const id = new Date().getTime(); // Unix Timestamp
        this.tempData.id = id;
        this.shopDatas.push(this.tempData);
        this.apiAddNewData(this.tempData);
      }
      //產品表單回復空值
      this.tempData = JSON.parse(JSON.stringify(this.baseData));
      $("#editModal").modal("hide");
    },

    //編輯資料
    editData(data = this.baseData) {
      this.tempData = JSON.parse(JSON.stringify(data));
      $("#editModal").modal("show");
    },
    //編輯資料api
    apiEditData(id) {
      this.$parent.isLoading = true;
      const url = `${baseUrl}/admin/ec/product/${id}`;
      this.$http.patch(url, this.tempData).then(() => {
        this.getData();
      });
    },
    //新增資料api
    apiAddNewData(data) {
      this.$parent.isLoading = true;
      const url = `${baseUrl}/admin/ec/product`;
      this.$http.post(url, data).then(() => {
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
      this.shopDatas.forEach((shopData, idx) => {
        if (shopData.id === id) {
          this.shopDatas.splice(idx, 1);
        }
      });
      this.tempDelete.id = "";
      this.tempDelete.title = "";
      $("#deleteModal").modal("hide");
    },
    apiDeleteData(id) {
      this.$parent.isLoading = true;
      //api
      const url = `${baseUrl}/admin/ec/product/${id}`;
      this.$http.delete(url).then(() => {
        this.getData();
      });
    },
    //上傳
    uploadFile() {
      this.$parent.isLoading = true;

      const uploadedFile = this.$refs.file.files[0];
      const formData = new FormData();
      formData.append("file", uploadedFile);
      const url = `${baseUrl}/admin/storage`;
      this.$http
        .post(url, formData, {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        })
        .then(response => {
          this.$parent.isLoading = false;
          if (response.status === 200) {
            this.tempData.imageUrl.push(response.data.data.path);
          }
        })
        .catch(() => {
          this.$parent.isLoading = true;
        });
    }
  }
};
</script>
