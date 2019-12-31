<template>
  <el-card>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="date" label="序号" type="index"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="visits" label="阅读数"></el-table-column>
      <el-table-column prop="state" label="状态" :formatter="geshi"></el-table-column>
      <el-table-column prop="creator" label="录入人"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">预览</el-button>
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          <el-button
            size="mini"
            @click="handleState(scope.$index, scope.row)"
            type="warning"
          >{{scope.row.state|dtzh}}</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-row type='flex' justify='end'>
         <el-pagination
    layout="prev, pager, next"
    :current-page='page.currentpage'
    :page-sizes='page.pagesizes'
    :total="page.total"
    @current-change='changepage'>
  </el-pagination>
    </el-row>
    <el-dialog :visible.sync="dialogVisible" title="收货地址"></el-dialog>
  </el-card>
</template>

<script>
import {
  list as articl_list,
  state as article_state,
  remove as article_remove
} from "../../api/hmmm/articles";
export default {
  data() {
    return {
        dialogVisible:false,//弹窗显示
      tableData: [],
      page:{
          currentpage:1,
          total:0,
          pagesize:10,
      },
      keyword:null
    };
  },
  methods: {
      //切换页码
      changepage(newval){
          this.page.currentpage=newval
          this.getarticle()
      },
    //请求列表
    getarticle() {
        let data={
            page:this.page.currentpage,
            pagesize:this.page.pagesize,
            keyword:this.keyword
        }
      articl_list(data).then(res => {
        console.log("定义一个", res.data);
        this.tableData = res.data.items;
        this.page.total=res.data.counts;
      });
    },
    //格式话状态
    geshi(row, column, cellValue, index) {
      if (cellValue == 1) {
        return "启用";
      } else {
        return "禁止";
      }
    },
    //状态修改
    handleState(index, row) {
      console.log(index, row);
      this.$confirm(`此操作将${row.state ? '屏蔽' : '开启'}状态, 是否继续?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let data = { id: row.id, state: row.state ? 0 : 1 };
        article_state(data).then(res => {
          console.log(res);
          this.getarticle();
        });
      });
    },
    //编辑
    handleEdit(index, row) {
        this.dialogVisible=!this.dialogVisible
      console.log(index, row);
    },
    //删除
    handleDelete(index, row) {
      console.log(index, row);
      this.$confirm(`此操作将删除该文章, 是否继续?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let data = { id: row.id };
        article_remove(data).then(res => {
          console.log(res);
          this.getarticle();
        });
      });
    }
  },
  filters: {
    dtzh: function(val) {
      return val ? "屏蔽" : "开启";
    }
  },
  created() {
    this.getarticle();
  }
};
</script>

<style>
</style>