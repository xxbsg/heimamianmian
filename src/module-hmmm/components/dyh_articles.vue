<template>
  <el-card>
   <el-row>
      <span style="margin-right:7px">关键字</span>
        <el-input v-model="keyword" style="width:15%;margin-right:12px"></el-input>
        <el-button @click="clear">清除</el-button>
        <el-button type="primary" @click="search">搜索</el-button>
      
    </el-row>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="date" label="序号" type="index"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="visits" label="阅读数"></el-table-column>
      <el-table-column prop="state" label="状态" :formatter="geshi"></el-table-column>
      <el-table-column prop="creator" label="录入人"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleyl(scope.$index, scope.row)">预览</el-button>
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
    <el-row type="flex" justify="end">
      <el-pagination
        layout="prev, pager, next"
        :current-page="page.currentpage"
        :page-sizes="page.pagesizes"
        :total="page.total"
        @current-change="changepage"
      ></el-pagination>
    </el-row>
    <el-dialog :visible.sync="dialogVisible">
      <el-form ref="form" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="标题" prop="title">
          <el-input v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="文章正文" prop="articleBody" >
          <!-- <el-input v-model="form.articleBody"></el-input> -->
          <quillEditor v-model="form.articleBody" style="height:400px;margin-bottom:100px">

          </quillEditor>
        </el-form-item>
        <el-form-item label="视频地址" prop='videoURL'>
          <el-input v-model="form.videoURL"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-row type="flex" justify="end">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="edit">确 定</el-button>
        </el-row>
      </span>
    </el-dialog>
    <el-dialog :visible.sync="dialogVisibleyl" >
      <el-card style="height:500px;overflow:auto;margin:0;box-shadow: 0">
        <!-- <el-row>{{form}}</el-row> -->
      <el-row><h4>{{form.title}}</h4></el-row>
      <el-row>{{form.cname}}</el-row>
      <hr>
      <el-row v-html="form.articleBody"></el-row>
      </el-card>
      
    </el-dialog>
  </el-card>
</template>

<script>
import { quillEditor } from "vue-quill-editor"; //调用编辑器
// 引入样式，此时样式是直接从quill文件中直接引入
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

import {
  list as articl_list,
  state as article_state,
  remove as article_remove,
  detail as article_detail,
  update as article_update
} from "../../api/hmmm/articles";
export default {
  data() {
    return {
      searchkey:'',
      form: {
        articleBody: "",
        id: null,
        title: "",
        videoURL: ""
      }, //编辑信息
      dialogVisible: false, //弹窗显示
      dialogVisibleyl:false,//预览
      tableData: [],
      page: {
        currentpage: 1,
        total: 0,
        pagesize: 10
      },
      keyword: null,
      rules: {
          title: [
            { required: true, message: '请输入标题名称' },
            
          ],
          articleBody: [
            { validator(rule, val, callback){
              console.log(1111111111111111111111111);
              
              if(val==''){
                callback(new Error('内容不能为空'));
              }else{
                callback()
              }
            } }
          ],
          videoURL: [
            { required: true, message: '不许为空'}
          ]}
    };
  },
   components: { quillEditor },
  methods: {
    //搜索
    search(){
      this.page.currentpage=1
    this.getarticle()
    },
    //清除搜索
    clear(){
      this.keyword=''
      this.getarticle()
    },
    //打开预览
    handleyl(index, row){
      this.dialogVisibleyl=true
       this.getarticle_xq(row.id,row.creator)
      //  this.form.createname=row.creator
      // this.$set(this.form, 'createnameaa', row.creator)
       console.log(this.form);
    },
    //修改
    edit(){
      article_update(this.form).then(res=>{
        // console.log(res);
        this.getarticle();
        this.dialogVisible = false
      })
      
      
    },
    //获取文章详情
    getarticle_xq(id,name=null) {
      let data = { id };
      article_detail(data).then(res => {
        console.log(res.data);
        this.form = res.data;
        this.$set(this.form,'cname',name)
      });
    },
    //切换页码
    changepage(newval) {
      this.page.currentpage = newval;
      this.getarticle();
    },
    //请求列表
    getarticle() {
      let data = {
        page: this.page.currentpage,
        pagesize: this.page.pagesize,
        keyword: this.keyword
      };
      articl_list(data).then(res => {
        console.log("定义一个", res.data);
        this.tableData = res.data.items;
        this.page.total = res.data.counts;
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
      this.$confirm(
        `此操作将${row.state ? "屏蔽" : "开启"}状态, 是否继续?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        }
      ).then(() => {
        let data = { id: row.id, state: row.state ? 0 : 1 };
        article_state(data).then(res => {
          console.log(res);
          this.getarticle();
        });
      });
    },
    //编辑
    handleEdit(index, row) {
      this.dialogVisible = !this.dialogVisible;
      this.getarticle_xq(row.id);
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
span{
  
}
</style>