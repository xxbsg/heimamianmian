<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row>
        <el-button type="primary" @click="dialogVisible = true">
          <i class="el-icon-plus">新建目录</i>
        </el-button>
        <el-button type="primary">
          <i>返回学科</i>
        </el-button>
      </el-row>
      <el-card class="title card">
        <span>目录名称:</span>
        <el-input style="width:200px" v-model="search.directoryName"></el-input>
        <span>状态:</span>
        <el-select placeholder="请选择" v-model="search.state">
          <el-option
            v-for="item in status"
            :key="item.value"
            :value="item.value"
            :label="item.label"
          ></el-option>
        </el-select>
        <el-button class="btn" @click="clear">清除</el-button>
        <el-button type="primary" class="btn" @click="about">搜索</el-button>
      </el-card>
      <el-card class="card">
        <el-table :data="list">
          <el-table-column prop="id" label="序号"></el-table-column>
          <el-table-column prop="directoryName" label="目录名称"></el-table-column>
          <el-table-column prop="creator" label="创建者"></el-table-column>
          <el-table-column prop="addDate" label="日期">
            <template slot-scope="obj">{{obj.row.addDate | parseTimeByString}}</template>
          </el-table-column>
          <el-table-column prop="totals" label="面试题数量" align="center"></el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="obj">{{ obj.row.state === 1 ? '启用' :'禁用' }}</template>
          </el-table-column>
          <el-table-column label="操作">
            <template slot-scope="obj">
              <el-button type="text" size="small" @click="modify(obj.row.id)">修改</el-button>
              <el-button
                type="text"
                size="small"
                @click="changeState(obj.row)"
              >{{obj.row.state === 1 ? '禁用' : '开启'}}</el-button>
              <el-button type="text" size="small" @click="del(obj.row.id)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-row style="height:60px" type="flex" justify="center" align="middle">
          <el-pagination
            layout="total, prev, pager, next"
            :total="page.total"
            :current-page="page.currentPage"
            :page-size="page.pageSize"
            @current-change="changePage"
          ></el-pagination>
        </el-row>
        <el-dialog :visible="dialogVisible" :show-close="false">
          <el-form label-width="80px" :model="fromData" :rules="rules" ref="myForm">
            <el-form-item label="目录名称" prop="directoryName">
              <el-input style="width:30%" v-model="fromData.directoryName"></el-input>
            </el-form-item>
            <el-form-item label="学科ID" prop="subjectID">
              <el-select style="width:30%" v-model="fromData.subjectID">
                <el-option
                  v-for="item of sub"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <el-row slot="footer" type="flex" justify="end">
            <el-button type="primary" @click="submit">确定</el-button>
            <el-button @click="cancel">取消</el-button>
          </el-row>
        </el-dialog>
      </el-card>
    </div>
  </div>
</template>

<script>
import {
  list,
  removeState,
  remove,
  add,
  detail,
  update
} from "../../api/hmmm/directorys";
import { status } from "../../api/hmmm/constants";
import { simple } from "../../api/hmmm/subjects";
export default {
  name: "DirectorysList",
  data() {
    return {
      list: [], //列表
      sub: [], //用来接收学科数据
      status,
      dialogVisible: false,
      search: {
        //搜索对象
        directoryName: "",
        state: ""
      },
      page: {
        //分页
        currentPage: 1,
        pageSize: 10,
        total: 0
      },
      fromData: {
        //添加
        subjectID: "",
        directoryName: ""
      },
      rules: {
        directoryName: [
          { required: true, message: "不能为空", trigger: "blur" }
        ],
        subjectID: [{ required: true, message: "不能为空", trigger: "blur" }]
      }
    };
  },
  methods: {
    async getDiretorys(data) {
      //获取文章列表
      let res = await list({
        //携带参数
        page: this.page.currentPage,
        pagesize: this.page.pageSize,
        ...data
      });
      this.list = res.data.items;
      this.page.total = res.data.counts;
      console.log(res.data);
    },
    clear() {
      //清除
      this.search = {
        directoryName: "",
        state: ""
      };
      this.getDiretorys();
    },
    about() {
      //搜索
      this.page.currentPage = 1;
      this.getDiretorys(this.search);
    },
    async del(id) {
      //删除
      await this.$confirm("你确定删除？");
      //确定删除 调用接口
      await remove({ id });
      this.$message({ type: "success", message: "删除成功" });
      this.getDiretorys(this.search); //携带状态删除 当前状态下 而不是有跳转
    },
    async changeState(row) {
      //状态改变
      await this.$confirm("确定关闭？");
      await removeState({ id: row.id, state: row.state === 1 ? 0 : 1 });
      this.getDiretorys();
    },
    async submit() {
      //弹层确定提交
      await this.$refs.myForm.validate();
      this.fromData.id ? await update(this.fromData) : await add(this.fromData);
      this.$message({ type: "success", message: "操作成功" });
      this.cancel();
      this.getDiretorys(); //是否携带状态参数看需求 同删除
    },
    async getSub() {
      //获取学科 弹层
      let res = await simple();
      this.sub = res.data;
    },
    cancel() {
      //弹层取消编辑
      this.fromData = {
        subjectID: "",
        directoryName: ""
      };
      this.dialogVisible = false;
    },
    async modify(id) {
      //修改 通过id获取目录详情
      let res = await detail({ id });
      this.fromData = res.data;
      this.dialogVisible = true;
    },
    changePage(newPage) {
      //页码
      this.page.currentPage = newPage;
      this.getDiretorys();
    }
  },
  created() {
    this.getDiretorys();
    this.getSub();
  }
};
</script>

<style scoped>
.title span {
  font-size: 12px;
  margin: 0 20px;
}
.card {
  margin-top: 20px;
}
.btn {
  margin-left: 10px;
}
</style>
