<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button type='primary' @click="dialogVisible = true"><i class="el-icon-plus"></i> 新增标签</el-button>
      <el-card class="card">
        <el-form inline>
          <el-form-item label="标签名称">
            <el-input v-model="searchForm.tagName"></el-input>
          </el-form-item>
          <el-form-item label="状态">
            <el-select v-model="searchForm.state">
              <el-option v-for="item in status" :key="item.value" :value="item.value" :label="item.label"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button @click="clear">清除</el-button>
            <el-button @click="search" type='primary'>搜索</el-button>
          </el-form-item>
        </el-form>
      </el-card>
        <el-table :data="list">
          <el-table-column
        prop='id'
        label="序号"
        width="100">
      </el-table-column>
      <el-table-column
        align="center"
        prop='tagName'
        label="标签名称">
      </el-table-column>
      <el-table-column
        prop='creator'
        label="创建者">
      </el-table-column>
      <el-table-column
        label="创建日期">
        <template slot-scope="scope">
          {{ scope.row.addDate | parseTimeByString }}
        </template>
      </el-table-column>
      <el-table-column
        prop='totals'
        align="center"
        label="面试题数量">
      </el-table-column>
      <el-table-column
        label="状态">
        <template slot-scope="scope">
          {{ scope.row.state ? '开启' : '禁用' }}
        </template>
      </el-table-column>
      <el-table-column
        label="操作">
        <template slot-scope="scope">
        <el-button @click="modify(scope.row.id)" size="mini" type="text">修改</el-button>
          <el-button type="text" size="mini" @click="changeStatus(scope.row)">{{ scope.row.state === 1 ? '禁用' : '开启' }}</el-button>
        <el-button size="mini" type="text" @click="delTag(scope.row.id)">删除</el-button>
      </template>
      </el-table-column>
        </el-table>
        <el-row type='flex' justify='center' style="height:60px;" align='middle'>
           <el-pagination
           :current-page="page.currentPage"
      :page-sizes="[10, 20, 30, 40]"
      :page-size="page.pageSize"
      layout="prev, pager, next, sizes, jumper"
      :total="page.total">
    </el-pagination>
        </el-row>
        <!-- 弹层 -->
        <el-dialog
        title="提示"
        :visible.sync="dialogVisible"
        width="30%">
        <span>添加标签</span>
        <input type="text" v-model="formData.tagName"><br>
          <span>学科标签</span>
          <el-select v-model="formData.subjectID">
            <el-option v-for="item in simple" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
          <span slot="footer" class="dialog-footer">
            <el-button @click="cancel">取 消</el-button>
            <el-button type="primary" @click="tagsAdd">确 定</el-button>
          </span>
        </el-dialog>
    </div>
  </div>
</template>

<script>
import { list,simple as simpleTag, add, remove, removeState, detail, update as updateTag } from "../../api/hmmm/tags";
import { simple } from "../../api/hmmm/subjects";
import { status } from '../../api/hmmm/constants'
export default {
  name: 'TagsList',
  data() {
    return {
      searchForm: {
        state: null,
        tagName: ''
      },
      formData: {
      subjectID:  null,
      tagName: ''
      },
      list: [],
      page: {
        currentPage: 1,
        pageSize: 10,
        total: 0
      },
      dialogVisible: false,
      simple: [],
      status  // 状态数据
    }
  },
  methods: {
    modify (id) {
      detail({id}).then(res => {
        this.formData = res.data
        this.dialogVisible = true
      })
    },
    getList (data) {
      list({
        page: this.page.currentPage,
        pagesize: this.page.pageSize,
        ...data
      }).then(res => {
        this.list = res.data.items
        this.page.total = res.data.counts
      }).catch(() => {})
    },
    search () {
      this.page.currentPage = 1
      this.getList(this.searchForm)
    },
    clear () {
      this.searchForm = {
        state: null,
        tagName: ''
      }
    },
    async tagsAdd () {
      this.formData.id ? await updateTag(this.formData) : await add(this.formData)
        this.$message({
          type: 'success',
          message: '操作成功'
        })
        this.getList(this.searchForm)
        this.cancel()
    },
    cancel () {
      this.formData = {
        subjectID:  null,
        tagName: ''
      }
      this.dialogVisible =false
    },
    getSelect () {
      simple().then(res => {
        this.simple = res.data
      })
    },
    delTag (id) {
      this.$confirm('确认删除本标签？').then(() => {
        remove({id}).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功'
          })
        this.getList(this.searchForm)
      })
      })
    },
    changeStatus (row) {
      removeState({id: row.id, state: row.state === 1 ? 0 : 1}).then(() => {
        this.getList(this.searchForm)
        this.$message({
          type: 'success',
          message: '改变状态成功'
        })
      })
    }
  },
  created () {
    this.getList()
    this.getSelect()
  }
}
</script>

<style scoped>
.card{
  margin: 30px 0;
}
</style>
