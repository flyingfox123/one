<template>
  <transition name="zoom">
    <div class="modal" v-show="config.show" style="display: block">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <button type="button" class="close" data-dismiss="modal" aria-label="Close" @click="config.show = false">
              <span aria-hidden="true">×</span>
            </button>
            <h4 class="modal-title">{{ config.title }}</h4>
          </div>
          <div class="modal-body modal-scrollable">
            <div class="row row-margin-bottom">
              <div class="col-md-12">
                <SupplierQueryCondition v-model="param" :purchaseType="purchaseType"></SupplierQueryCondition>
              </div>
            </div>
            <div class="row">
              <div class="col-md-12">
                <table class="table table-bordered table-hover">
                  <thead>
                  <tr>
                    <th style="width: 50px"><input type="checkbox" v-model="isSelectAll"></th>
                    <th>供应商名称</th>
                    <th>注册时间</th>
                    <th>联系人</th>
                    <th>手机号码</th>
                    <th>状态</th>
                  </tr>
                  </thead>
                  <tbody>
                  <tr v-for="(obj, index) of page.list">
                    <td><input type="checkbox" :value="obj" v-model="selectedSupplierList" :key="obj.supplierId"></td>
                    <td>{{ obj.name }}</td>
                    <td>{{ obj.createTime }}</td>
                    <td>
                      <UserInfoSimpleView :user="obj.principalUser"></UserInfoSimpleView>
                    </td>
                    <td>{{ obj.principalUser.userContactInfo.phone }}</td>
                    <td>
                      <UserStatusLabel :status="obj.status" :desc="obj.statusCn"></UserStatusLabel>
                    </td>
                  </tr>
                  </tbody>
                </table>
                <Pagination :page="page" @page="handlerPage(arguments)"></Pagination>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <div class="pull-left">
              选中
              <template v-for="supplier of selectedSupplierList">
                <span class="label label-info">{{ supplier.name }}</span>&nbsp;
              </template>
            </div>
            <button type="button" class="btn btn-default " data-dismiss="modal" @click="config.show = false">取消</button>
            <button type="button" class="btn btn-primary" @click="ok()">确认</button>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import PageMixin from '../../../../../common/mixins/PageMixin.js'
  import UserInfoSimpleView from '../common/UserInfoSimpleView'
  import UserStatusLabel from '../common/UserStatusLabel'

  export default {
    mixins: [PageMixin],
    components: {
      UserInfoSimpleView,
      UserStatusLabel
    },
    props: {
      config: {
        type: Object,
        required: true
      },
      purchaseType: {
        type: Object,
        required: true
      },
      value: {}
    },
    data: () => {
      return {
        actions: {
          list: {method: 'get', url: '/api/user/supplier'}
        },
        param: {
          supplierStatus: 2,
          purchaseTypeId: ''
        },
        selectedSupplierList: [],
        isSelectAll: false
      }
    },
    methods: {
      ok () {
        this.$emit('input', this.selectedSupplierList)
        this.config.show = false
        this.isSelectAll = false
      }
    },
    watch: {
      'isSelectAll': function () {
        if (this.isSelectAll) {
          this.selectedSupplierList = this.page.list
        } else {
          this.selectedSupplierList = []
        }
      },

      /**
       * 刷新数据的时候重置选中的用户
       */
      'page': {
        handler: function () {
          this.selectedSupplierList = []
        },
        deep: true
      }
    }
  }
</script>
