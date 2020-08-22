<template>
  <div id="storages" class="container">
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120" class="text-center">編號</th>
          <th class="text-center">圖片預覽</th>
          <th width="120" class="text-center">操作</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in storages" :key="item.id">
          <td class="text-center">{{ index + 1 }}</td>
          <td class="text-center">
            <img :src="item.path" width="100px" />
          </td>
          <td class="text-center">
            <div class="btn-group">
              <button class="btn btn-outline-danger btn-sm" @click="deleteQA(item)">
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- pagination -->
    <Pagination :pages="pagination" @emit-pages="getStorages" name="pagination"></Pagination>

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
  </div>
</template>

<script>
/* global $*/
import Pagination from "@/components/_shared/admin/Pagination";
const getStorage = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/admin/storage`;
export default {
  components: {
    Pagination
  },
  data() {
    return {
      pageSettings: {
        page: 1,
        paged: 10
      },
      pagination: {
        current_page: 1
      },
      storages: [],
      isLoading: false,
      tempDelete: {}
    };
  },
  mounted() {
    this.getStorages();
  },
  methods: {
    getStorages(page = 1) {
      this.$parent.isLoading = true;
      this.pageSettings.page = page;
      this.$http
        .get(`${getStorage}`, { params: this.pageSettings })
        .then(response => {
          this.storages = response.data.data;
          this.pagination = response.data.meta.pagination;
          this.$parent.isLoading = false;
        })
        .catch(() => {
          this.$parent.isLoading = false;
        });
    },
    deleteQA(data) {
      this.tempDelete = data;
      $("#deleteModal").modal("show");
    },
    deleteData() {
      this.$parent.isLoading = true;
      const url = `${process.env.VUE_APP_PATH}${process.env.VUE_APP_UUID}/admin/storage/${this.tempDelete.id}`;
      this.$http.delete(url).then(() => {
        $("#deleteModal").modal("hide");

        this.$parent.isLoading = false;
        this.getStorages();
      });
    }
  }
};
</script>

<style lang="scss"></style>
