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
  </el-card>
</template>

<script>
import {
  list as articl_list,
  state as article_state
} from "../../api/hmmm/articles";
export default {
  data() {
    return {
      tableData: []
    };
  },
  methods: {
    //请求列表
    getarticle() {
      articl_list().then(res => {
        console.log("定义一个", res.data);
        this.tableData = res.data.items;
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
    handleEdit(index, row) {
      console.log(index, row);
    },
    handleDelete(index, row) {
      console.log(index, row);
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