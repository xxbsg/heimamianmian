<template>
  <div class="dashboard-container">
    <el-card>
      <el-row slot="header">
        <el-button type="primary" @click="dialogVisible=true">新增学科</el-button>
      </el-row>
      <el-row>
        <el-form :inline="true" label-position="right" label-width="80px">
          <el-form-item label="学科名称" >
            <el-input :autofocus='true' v-model="searchSubject"></el-input>
          </el-form-item>
          <el-button @click="searchSubject=''">清除</el-button>
          <el-button type="primary" @click="searchSubjects">搜索</el-button>
        </el-form>
      </el-row>
      <!-- 表格列表 -->
      <el-table :data='subjectList' style="width:100%">
        <el-table-column label="序号" prop="id"></el-table-column>
        <el-table-column label="学科名称" prop="subjectName"></el-table-column>
        <el-table-column label="创建者" prop="username"></el-table-column>
        <el-table-column label="创建日期" prop="addDate">
          <template slot-scope="obj">{{obj.row.addDate|parseTimeByString}}</template>
        </el-table-column>
        <el-table-column label="前台是否显示" prop="isFrontDisplay">
          <template  slot-scope="obj">{{obj.row.isFrontDisplay?'显示':'-'}} </template>
        </el-table-column>
        <el-table-column label="二级目录" prop="twoLevelDirectory"></el-table-column>
        <el-table-column label="标签" prop="tags"></el-table-column>
        <el-table-column label="题目数量" prop="totals"></el-table-column>
        <!-- 操作 -->
        <el-table-column  label="操作" width="220px" >
          <template slot-scope="scope">
           <el-link :underline='false' style="font-size:8px;margin-right:10px ;" type="primary">学科分类</el-link>
           <el-link :underline='false' style="font-size:8px;margin-right:10px;" type="primary">学科标签</el-link>
           <el-link :underline='false' style="font-size:8px;margin-right:10px;" type="primary" @click='editSubject(scope.row.id,scope.row.subjectName)'>修改</el-link>
           <el-link :underline='false' style="font-size:8px;margin-right:10px;" type="primary" @click='delSubject(scope.row.id)'>删除</el-link>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-row type="flex" justify="end" style="margin-top:20px">
        <span style="height:35px;line-height:35px;font-size:8px">共{{page.counts}}条</span>
        <el-pagination
          background
          layout="prev, pager, next"
          :page-size=page.pagesize
          :current-page=page.page
          :total=page.counts
          @current-change='changePage'>
        </el-pagination>
      </el-row>


      <!--  -->
      <el-dialog :visible="dialogVisible" width="50%" :show-close='false'>
          <el-form label-position="right" label-width="150px" ref="myForm" :model="ruleForm" :rules="rules">
              <el-form-item label="学科名称" prop="subjectName" >
                <el-input style="width:90%;" v-model="ruleForm.subjectName"></el-input>
              </el-form-item>
              <el-form-item label="是否前台显示" prop="isFrontDisplay">
                <el-switch v-model="ruleForm.isFrontDisplay">是否前台显示</el-switch>
              </el-form-item>
          </el-form>
          <el-row slot="footer" type="flex" justify="end">
            <el-button type="primary" @click="success">确定</el-button>
            <el-button @click="closeDialog">取消</el-button>
          </el-row>
      </el-dialog>
    </el-card>
  </div>
</template>
<script>
import {list as subjectList,remove,simple,update,add} from '../../api/hmmm/subjects'
export default {
  name: 'SubjectsList',
  data() {
    return {
      subjectList:[],
      page:{
        pagesize:10,
        page:1,
        counts:0
      },
      dialogVisible:false,
      ruleForm:{
        subjectName:'',
        isFrontDisplay:true,
      },
      rules:{
        subjectName:[{required: true, message: '学科名称不能为空', trigger: 'blur'}]
      },
      subjectId:null,
      searchSubject:'',
    }
  },
  methods:{
    // 获取学科列表内容
    async getSubjectList(params){
      let res=await subjectList(params)
      this.subjectList=res.data.items
      this.page=res.data
    },
    // 分页功能
    changePage(newPage){
      this.page.page=newPage
      let params={
        pagesize:this.page.pagesize,
        page:this.page.page
      }
      this.getSubjectList(params)
    },
    // 删除学科
    async delSubject(id){
      await this.$confirm('您确定要删除吗？')
      await remove({id})
      this.$message({
        type:'success',
        message:'删除成功'
      })
      this.getSubjectList()
    },
    // 修改学科
    async editSubject(id,subjectName){
      this.dialogVisible=true
      this.ruleForm.subjectName=subjectName
      this.ruleForm.isFrontDisplay=true
      this.subjectId=id
    },
    closeDialog(){
      this.dialogVisible=false,
      this.subjectId=null
      this.ruleForm.subjectName=''
      this.ruleForm.isFrontDisplay=false
    },
    // 判断修改还是新增
    async success(){
      await this.$refs.myForm.validate()
      if(this.subjectId===null){
        // 新增
        this.ruleForm.isFrontDisplay=false
        await add(this.ruleForm)
        this.$message({
        type: "success",
        message: "新增成功"
      });
      this.closeDialog(),
      this.getSubjectList()
      }else{
        // 修改
        this.ruleForm.id=this.subjectId
        await update(this.ruleForm)
        this.$message({
        type: "success",
        message: "修改成功"
      });
      this.closeDialog(),
      this.getSubjectList()
      }
    },
    // 搜索学科
    searchSubjects(){
      this.page.page=1,
      this.getSubjectList({subjectName:this.searchSubject})
    }
  },
  created(){
    this.getSubjectList()
  }
}
</script>
<style scoped>
</style>