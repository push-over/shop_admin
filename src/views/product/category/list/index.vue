<template>
  <div class="app-container">
    <div class="filter-container">
      <router-link :to="'/product/category/create'">
        <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-plus">
          创建
        </el-button>
      </router-link>
    </div>
    <el-table :key="tableKey" v-loading="listLoading" :data="list" row-key="_id" border fit highlight-current-row :tree-props="{children: 'children'}"> style="width: 100%">
      <el-table-column align="center" label="分类ID">
        <template slot-scope="scope">
          <span>{{ scope.row._id }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="分类 Icon">
        <template slot-scope="scope">
          <img width="100" :src="scope.row.icon" alt="">
        </template>
      </el-table-column>
      <el-table-column align="center" label="分类名称">
        <template slot-scope="scope">
          <span>{{ scope.row.title }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="创建时间">
        <template slot-scope="scope">
          <span>{{ scope.row.created_at | parseTime('{y}-{m}-{d} {h}:{i}') }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" label="操作">
        <template slot-scope="{row}">
          <router-link :to="'/product/category/edit/'+row._id">
            <el-button type="primary" size="small" icon="el-icon-edit">
              修改
            </el-button>
          </router-link>
          <el-button v-if="row.status!='deleted'" size="small" type="danger" icon="el-icon-delete" @click="handleDelete(row)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { getProductCategory, deleteProductCategory } from '@/api/product'
import waves from '@/directive/waves'

export default {
  name: 'ProductCategory',
  directives: { waves },
  data() {
    return {
      tableKey: 0,
      list: [],
      listLoading: true
    }
  },
  created() {
    this.getList()
  },
  methods: {
    getList() {
      this.listLoading = true
      getProductCategory().then(response => {
        this.list = response
        this.listLoading = false
      })
    },
    handleDelete(row) {
      this.$confirm('This will permanently delete the file. Do you want to continue?', 'prompt', {
        confirmButtonText: 'Confirm',
        cancelButtonText: 'Cancel',
        type: 'warning'
      }).then(() => {
        deleteProductCategory(row._id).then(response => {
          this.$notify({
            title: '成功',
            message: '删除成功',
            type: 'success',
            duration: 2000
          })
          const index = this.list.indexOf(row)
          this.list.splice(index, 1)
          this.getList()
        })
      }).catch(() => {
        this.$notify({
          title: 'info',
          message: '已取消删除',
          type: 'info',
          duration: 2000
        })
      })
    }
  }
}
</script>

<style scoped>
.edit-input {
  padding-right: 100px;
}
.cancel-btn {
  position: absolute;
  right: 15px;
  top: 10px;
}
</style>
