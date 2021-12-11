<template>
  <div class="app-container">

    <AuthingGuard :appId="appId" @login="handleLogin" :isSSO="true" :visible="visible" mode="modal" :clickCloseable="false" :escCloseable="false"/>

    <el-button @click="handleLogout" type="warning" >退出登录</el-button>
    <a href="https://github.com/Yasuomvp/WebCourseManageSystem" >GuoShibiao@Github
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-github" viewBox="0 0 16 16">
        <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.012 8.012 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
      </svg>
    </a>
    <div class="add-form">
      <el-dialog title="新增公告" :visible.sync="dialogFormVisible">
        <template>
          <el-tabs type="card">
            <el-form label-position="right" label-width="100px">
              <el-row>
                <el-col :span="24">
                  <el-form-item label="标题">
                    <el-input v-model="formData.title" />
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="24">
                  <el-form-item label="文本">
                    <el-input v-model="formData.text" type="textarea" :rows="10" />
                  </el-form-item>
                </el-col>
              </el-row>
            </el-form>
          </el-tabs>
        </template>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取消</el-button>
          <el-button type="primary" @click="handleAdd()">提交</el-button>
        </div>
      </el-dialog>
    </div>

    <div class="edit-form">
      <el-dialog title="编辑公告" :visible.sync="dialogFormVisible4Edit">
        <template>
          <el-tabs type="card">
            <el-form label-position="right" label-width="100px">
              <el-row>
                <el-col :span="24">
                  <el-form-item label="标题">
                    <el-input v-model="formData.title" />
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="24">
                  <el-form-item label="文本">
                    <el-input v-model="formData.text" type="textarea" :rows="10" />
                  </el-form-item>
                </el-col>
              </el-row>
            </el-form>
          </el-tabs>
        </template>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible4Edit = false">取消</el-button>
          <el-button type="primary" @click="handleEdit()">提交</el-button>
        </div>
      </el-dialog>
    </div>

    <el-table
        v-loading="listLoading"
        :data="list"
        element-loading-text="Loading"
        border
        fit
        highlight-current-row
    >
      <el-table-column align="center" label="序号" width="95">
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column>
      <el-table-column label="标题">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="图片名称" width="200" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.pic }}</span>
        </template>
      </el-table-column>
      <el-table-column label="文本" width="1000" align="center">
        <template slot-scope="scope">
          {{ scope.row.text }}
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="更改时间" width="200">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ scope.row.timestamp }}</span>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center">
        <template slot-scope="scope">
          <el-button size="mini" type="success" @click="handleCreate()">新增</el-button>
          <br>
          <br>
          <el-button type="primary" size="mini" @click="handleUpdate(scope.row)">编辑</el-button>
          <br>
          <br>
          <el-button size="mini" type="danger" @click="handleDelete(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

  </div>

</template>

<script>

import axios from 'axios'
import {AuthingGuard, getAuthClient} from '@authing/vue-ui-components'
import '@authing/vue-ui-components/lib/index.min.css'

import { initAuthClient } from '@authing/vue-ui-components'

initAuthClient({
  appId: '61b45f9acc9baf5d56a3a71e',
})

export default {
  components: {
    AuthingGuard,
  },
  data() {
    return {
      visible: true,
      appId: '61b45f9acc9baf5d56a3a71e',
      list: null,
      listLoading: false,
      dialogFormVisible: false,
      formData: {},
      dialogFormVisible4Edit: false
    }
  },
  created() {

  },
  methods: {
    handleLogout() {
      getAuthClient().logout()
      this.list = [];
      this.listLoading = true;

      this.$message({
        type: 'success',
        message: '你已退出登录,下次见~'
      })
    },
    handleLogin() {
      this.visible = false
      this.fetchData2()
    },
    handleDelete(row) {
      this.$confirm('你想删我东西？？？真的假的？？？[黑人疑惑脸]', '提示', { type: 'warning' }).then(
          () => {
            axios.get('http://localhost:8888/card/delete/' + row.id).then(
                (resp) => {
                  if (resp.data.flag) {
                    this.$message({
                      type: 'success',
                      message: resp.data.message
                    })
                  } else {
                    this.$message.error(resp.data.message)
                  }
                }
            ).finally(
                () => {
                  this.fetchData2()
                }
            )
          }
      ).catch(
          () => {
            this.$message({
              type: 'info',
              message: '操作已取消'
            })
          }
      )
    },
    handleUpdate(row) {
      this.dialogFormVisible4Edit = true
      this.formData = JSON.parse(JSON.stringify(row))
    },
    handleEdit() {
      axios.post('http://localhost:8888/card/edit', this.formData).then(
          (resp) => {
            this.dialogFormVisible4Edit = false
            if (resp.data.flag) {
              this.$message({
                type: 'success',
                message: resp.data.message
              })
            } else {
              this.$message.error(resp.data.message)
            }
          }
      ).finally(() => {
        this.fetchData2()
      })
    },
    handleAdd() {
      axios.post('http://localhost:8888/card/add', this.formData).then(
          (resp) => {
            this.dialogFormVisible = false
            if (resp.data.flag) {
              this.$message({
                message: resp.data.message,
                type: 'success'
              })
            } else {
              this.$message.error(resp.data.message)
            }
          }
      ).finally(
          () => {
            this.fetchData2()
          }
      )
    },
    fetchData2() {
      this.listLoading = true
      var _this = this
      // eslint-disable-next-line no-undef
      axios.get('http://localhost:8888/card/getall').then(
          (resp) => {
            _this.list = resp.data
          }
      )
      this.listLoading = false
    },
    handleCreate() {
      this.formData = {}
      this.dialogFormVisible = true
    }
  }
}
</script>
