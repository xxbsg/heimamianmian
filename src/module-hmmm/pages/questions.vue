<template>
  <div class="question-container">
    <el-card>
      <el-row class="header-btn" type="flex" align="middle">
        <el-button type="primary">新增试题</el-button>
        <el-button type="primary">试题导入</el-button>
      </el-row>
      <el-form class="select-list" label-width="100px" ref="myForm" :v-model="formData">       
        <el-form-item  style="margin-top:20px;" label="学科">
          <el-select  placeholder="请选择" v-model="formData.simpleValue">
            <el-option
              v-for="item in Simple"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
          <el-form-item style="margin-top:20px" label="难度">
        <el-select  placeholder="请选择" v-model="formData.difficultyValue"> 
        <el-option
           v-for="item in difficulty"   
           :key="item.value"
           :label="item.label"
           :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
               <el-form-item style="margin-top:20px" label="试题类型">
          <el-select v-model="formData.questionTypeValue" placeholder="请选择">
            <el-option
              v-for="item in questionType"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
               <el-form-item style="margin-top:20px" label="标签">
          <el-select  placeholder="请选择" v-model="formData.tagValue">
            <el-option
            v-for="item in tagName"
            :key="item.id"
            :value="item.id"
            :label="item.tagName"
            ></el-option>
          </el-select>
        </el-form-item>
         <el-form-item  style="margin-top:20px;" label="城市">
          <el-select  placeholder="请选择" v-model="formData.cityValue">
            <el-option
            v-for="(item,index) in provinces"
            :key="index"
            :label="item"
            :value="index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item  style="margin-top:20px;" label="区域">
          <el-select  placeholder="请选择" v-model="formData.areaValue">
            <el-option
          v-for="(item,index) in areas"
          :key="index"
          :label="item"
          :value="index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item  style="margin-top:20px;" label="关键字" prop="keyword">
          <el-input v-model="formData.keyword"></el-input>
        </el-form-item>
        <el-form-item  style="margin-top:20px;margin-left:15px" label="题目备注" prop="remarks">
          <el-input v-model="formData.remarks"></el-input>
        </el-form-item>
        <el-form-item  style="margin-top:20px;" label="企业简介" prop="remarks">
          <el-input v-model="formData.shortName"></el-input>
        </el-form-item>
        <el-form-item  style="margin-top:20px;" label="方向">
          <el-select  placeholder="请选择" v-model="formData.directionValue">
            <el-option
             v-for="(item,index) in direction"
             :key="index"
             :label="item"
             :value="index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item  style="margin-top:20px;" label="录入人">
          <el-select  placeholder="请选择" v-model="formData.userNameValue">
            <el-option
             v-for="(item,index) in userName"
             :key="index"
             :label="item"
             :value="index"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item  style="margin-top:20px;" label="二级目录">
          <el-select placeholder="请选择" v-model="formData.directoryValue">
            <el-option
            v-for="item in directoryName"
            :key="item.id"
            :label="item.directoryName"
            :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form> 
      <el-row type="flex" justify="center" style="margin:20px 0">
        <el-button @click="clear">清除</el-button>
        <el-button type="primary">搜索</el-button>
      </el-row>
      <el-table :data='question'>
        <el-table-column label="序号" prop="id"></el-table-column>
        <el-table-column label="试题编号" prop="number"></el-table-column>
        <el-table-column label="学科" prop="subjectID"></el-table-column>
        <el-table-column label="题型" prop="questionType" :formatter='questionFormatter'></el-table-column>
        <el-table-column label="题干" width="200px" prop="question"> </el-table-column>
        <el-table-column label="录入时间" prop="addDate" width="180px">
          <template slot-scope="obj">
            {{obj.row.addDate | parseTimeByString}}
          </template>
        </el-table-column>
        <el-table-column label="难度" prop="difficulty" :formatter="diffocultyFormatter"></el-table-column>
        <el-table-column label="录入人" prop="creator"></el-table-column>
        <el-table-column label="操作" width="180px">
          <template slot-scope="obj">
            <el-button type="text" size="small" @click="previewId(obj.row.id)">预览</el-button>
            <el-button type="text" size="small">修改</el-button>
            <el-button type="text" size="small" @click="delItem(obj.row.id)">删除</el-button>
            <el-button type="text" size="small" @click="joinItem(obj.row.id)">加入精选</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-row type="flex" justify="center" style="height:50px" align="middle">   
        <el-pagination background layout="prev, pager, next"
         :total="page.total"
          :page-size="page.pageSize"
          :current-page="page.currentPage"
           @current-change="changePage">
        </el-pagination>
      </el-row>
       <el-dialog :visible="visible" @close="closePreview" title="题目预览">
      <el-row style="height:60px" type="flex" align="middler">
        <el-col :span="6">题库:面试宝典题库</el-col>
        <el-col :span="6"></el-col>
        <el-col :span="6"></el-col>
        <el-col :span="6">学科:</el-col>
      </el-row>
      <el-row style="height:60px" type="flex" align="middler">
        <el-col :span="6">题型:</el-col>
        <el-col :span="6">编号:</el-col>
        <el-col :span="6">难度:</el-col>
        <el-col :span="6">标签:</el-col>
      </el-row>
      <el-row style="height:60px" type="flex" align="middler">
        <el-col :span="6">目录:</el-col>
        <el-col :span="6">企业:</el-col>
        <el-col :span="6">方向:</el-col>
        <el-col :span="6">城市: </el-col>
      </el-row>
      <el-row></el-row>
      <el-row slot="footer" type="flex" justify="end">
        <el-button type="primary" @click="closePreview">关闭</el-button>
      </el-row>
      <el-row style="margin:60px 0">
        题干:
      </el-row>
    </el-dialog>
    </el-card>
    
  </div>
</template>

<script>


import {simple} from '../../api/hmmm/subjects'
import{difficulty,questionType,direction}from '../../api/hmmm/constants'
import{list as questionList,detail,update,remove}from '../../api/hmmm/questions'
import{list as tagList} from '../../api/hmmm/tags'
import{provinces,citys}from '../../api/hmmm/citys'
import{list as directorysList} from '../../api/hmmm/directorys'
import { log } from 'util'
import { async } from 'q'
import { format } from 'url'
export default{
  
  data(){
    return {
      visible: false,
      Simple:[],
      difficulty,
      questionType,
      list:[],
      tagName:[],
      provinces:provinces(),
      citys:citys(),
      direction,
      userName:['超级管理员','编辑'],
      directoryName:'',
      question:[],
   
      // select value 值
      formData:{
      simpleValue:null,
      difficultyValue:null,
      questionTypeValue:null,
      tagValue:null,
      cityValue:null,
      areaValue:null,
      remarks:null,
      keyword:null,
      directionValue:null,
      userNameValue:null,
      directoryValue:null,
      remarks:null,
      },
      //  地区
       areas:[],
         // 分页
      page:{
        total:0,
        currentPage:1,
        pageSize:8
      }
    }
  },
  filters:{
    tixing(value){
      debugger;
       switch (value) {
        case 1:
          
         return '单选';
         case 2:
          
         return '多选';
         case 3:
          
         return '简单';
         
      
        default:
          return '未知'
      }
    }
  }
  ,
  watch:{
    cityValue:function(){
      
       this.areas=citys(this.provinces[this.cityValue])
    }
  },
  methods:{
    // 预览
       async previewId(id) {
      let res = await detail({ id });
      this.formData = res.data;
      this.visible = true;
    },
    closePreview(){
      this.visible = false
    },
    // 加入精选
    async joinItem(id) {
      await this.$confirm("是否加入精选");
    },
    changePage(newPage) {
      this.page.currentPage = newPage;
      this.getCondition();
    },
       getCondition() {
      let params = {
        page: this.page.currentPage,
        pageSize: this.page.pageSize,
        ...this.formData
      };
      this.getQuestion(params);
    },
    // 分页
    changePage(newPage){
      this.page.currentPage = newPage;
      this.getQuestion()
    },
    // 删除问题
    async delItem(id){
      await this.$confirm('是否删除')
      await remove({id})
      this.$message({type:'success',message:'删除成功'})
      this.getQuestion()
    },
    // 清除搜索框
    clear(){
      this.formData={}
    },
    // 学科类型

 
    diffocultyFormatter(row, column, cellValue, index){
      switch (cellValue) {
        case '1':
          return "简单";
          case '2':
          return "一般"
          case '3':
          return "困难"
        default:
          return "未知"
      }
    },
    // 题型过滤
    questionFormatter(row, column, cellValue, index){
      switch (cellValue) {
        case '1':
          return "单选";
          case '2':
          return "多选"
          case '3':
          return "简单"
        default:
          return "未知"
      }
    },
    // subjectFormatter(row, column, cellValue){
    //   let res = this.getQuestion.filter(item=>item.value==cellValue);
    //   return res.length? res[0].label : '未知'
    // },
    // 基础题库列表
    async getQuestion(){
      let res =await questionList()
      console.log(res);
      this.page.total = res.data.counts
     this.question=res.data.items 
    },
    
    // 获取目录
    async getDirectorys(){
      let res = await directorysList()
      this.directoryName=res.data.items
      
    },
    // 获取学科
   async getSimple(){
     let res =  await  simple() 
      this.Simple= res.data
    },
    // 获取标签详情
    async getTag(){
      let res = await tagList()
      this.tagName = res.data.items
      
      
     
      
    },
    
  },

  created(){
    this.getSimple()
    this.getTag()
   this.getDirectorys()
   this.getQuestion()
    
    
    

  }
}
</script>

<style lang="scss">
.header-btn {
  border-bottom: 1px solid #ebeef5;
  height: 50px;
  padding-bottom: 10px;
}
.select-list {
  display: flex;
  flex-wrap: wrap;
}

</style>
