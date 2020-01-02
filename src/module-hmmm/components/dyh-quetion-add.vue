<template>
  <el-card>
    <el-form :model="form" ref="myform" :rules="rules" label-width="80px">
      <el-form-item label="学科" prop="subjectID">
        <el-select v-model="form.subjectID" placeholder="请选择">
          <el-option
            v-for="item in list.subjects"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="目录" prop="catalogID">
        <el-select v-model="form.catalogID" placeholder="请选择">
          <el-option
            v-for="item in list.directorys"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="企业" prop="enterpriseID">
        <el-select v-model="form.enterpriseID" placeholder="请选择">
          <el-option
            v-for="item in list.companys"
            :key="item.id"
            :label="item.shortName"
            :value="item.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="城市" prop="province">
        <el-select v-model="form.province" placeholder="请选择" style="margin-right:8px">
          <el-option
            v-for="(item,index) in list.provinces"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
        <el-select v-model="form.city" placeholder="请选择">
          <el-option v-for="(item,index) in citylist" :key="index" :label="item" :value="item"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="方向" prop="direction">
        <el-select v-model="form.direction" placeholder="请选择">
          <el-option
            v-for="(item,index) in list.direction"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="题型" prop="questionType">
        <el-radio
          v-model="form.questionType"
          :label="item.value.toString()"
          v-for="item in list.questionType"
          :key="item.value"
        >{{item.label}}</el-radio>
        <!-- <el-radio v-model="form.questionType" label="2">备选项</el-radio> -->
      </el-form-item>
      <el-form-item label="难度" prop="difficulty">
        <el-radio
          v-model="form.difficulty"
          :label="item.value.toString()"
          v-for="item in list.difficulty"
          :key="item.value"
        >{{item.label}}</el-radio>
      </el-form-item>
      <el-form-item label="题干" prop="question">
        <quillEditor v-model="form.question" style="margin-bottom:56px;height:300px"></quillEditor>
      </el-form-item>
      <el-form-item label="选项(以下选中的选项为正确答案)" v-show="form.questionType=='3'?false:true">
        <el-radio-group v-model="list.radio" v-if="form.questionType=='1'" @change="danxuan">
          <el-radio
            :label="item"
            v-for="(item,index) in list.xxlist"
            :key="item.key"
            style="display:block;margin-bottom:30px"
          >
            <span>{{item.code=list.xuanx[index]}}</span>
            <el-input v-model="item.title"></el-input>
            <el-button @click="del(index)">删除该项</el-button>
          </el-radio>
        </el-radio-group>
        <el-checkbox-group
          v-model="list.checkList"
          v-else-if="form.questionType=='2'"
          @change="duoxuan"
        >
          <el-checkbox
            :label="item"
            v-for="(item,index) in list.xxlist"
            :key="item.key"
            style="display:block;margin-bottom:30px"
          >
            <span>{{item.code=list.xuanx[index]}}</span>
            <el-input v-model="item.title"></el-input>
            <el-button @click="del(index)">删除该项</el-button>
          </el-checkbox>
        </el-checkbox-group>
        <el-row>
          <el-button type="primary" @click="tianjia">增加选项与答案</el-button>
        </el-row>
      </el-form-item>
      <el-form-item label="解析视频" prop="videoURL">
        <el-input v-model="form.videoURL" style="width:55%"></el-input>
      </el-form-item>
      <el-form-item label="答案解析" prop="answer">
        <quillEditor v-model="form.answer" style="margin-bottom:56px;height:300px"></quillEditor>
      </el-form-item>
      <el-form-item label="题目备注" prop="remarks">
        <el-input v-model="form.remarks" style="width:55%"></el-input>
      </el-form-item>
      <el-form-item label="试题标签" prop="tags">
        <quillEditor v-model="form.tags" style="margin-bottom:56px;height:300px"></quillEditor>
      </el-form-item>
      <el-form-item>
        <el-row type="flex" justify="center" align="middle">
          <el-button type="primary" @click="tijiao">提交</el-button>
          <el-button @click="quxiao">取消</el-button>
        </el-row>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script>
import { quillEditor } from "vue-quill-editor"; //调用编辑器
// 引入样式，此时样式是直接从quill文件中直接引入
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";

import { simple as sub_simple } from "../../api/hmmm/subjects";
import { simple as dir_simple } from "../../api/hmmm/directorys";
import { list as com_list } from "../../api/hmmm/companys";
import { provinces, citys } from "../../api/hmmm/citys";
import { direction, questionType, difficulty } from "../../api/hmmm/constants";
import { add as que_add } from "../../api/hmmm/questions";
export default {
  data() {
    return {
      radio: "1",
      form: {
        subjectID: null, //学科
        catalogID: null, //目录
        enterpriseID: null, //企业
        province: null, //省份
        city: null, //城市
        direction: null, //方向
        questionType: "1", //题型
        difficulty: "1", //难度
        question: null, //题干
        videoURL: null, //解析视频
        answer: null, //答案解析
        remarks: null, //题目备注
        tags: null, //试题标签
        options: [] //选项
      },
      list: {
        subjects: null, //学科
        directorys: null, //目录
        companys: null, //企业
        provinces: provinces(), //省份
        // citys:[],//地区
        direction, //方向
        questionType, //题型
        difficulty, //难度
        xuanx: "abcdefghigklm", //选项
        xxlist: [], //遍历项
        js: 0,
        radio: "", //单选
        checkList: [] //复选
      },
      options: [],
      rules: {
        subjectID: [{ required: true, message: "不能为空" }], //学科
        catalogID: [{ required: true, message: "不能为空" }], //目录
        enterpriseID: [{ required: true, message: "不能为空" }], //企业
        province: [{ required: true, message: "不能为空" }], //省份
        city: [{ required: true, message: "不能为空" }], //城市
        direction: [{ required: true, message: "不能为空" }], //方向
        questionType: [{ required: true, message: "不能为空" }], //题型
        difficulty: [{ required: true, message: "不能为空" }], //难度
        question: [{ required: true, message: "不能为空" }], //题干
        videoURL: [{ required: true, message: "不能为空" }], //解析视频
        answer: [{ required: true, message: "不能为空" }], //答案解析
        remarks: [{ required: true, message: "不能为空" }], //题目备注
        tags: [{ required: true, message: "不能为空" }], //试题标签
        options: [] //选项
      }
    };
  },
  computed: {
    citylist() {
      //地区
      return citys(this.form.province);
    }
  },
  watch: {
    "form.questionType": function() {
      console.log(this.form.questionType);

      this.list.xxlist.forEach(item => {
        item.isRight = false;
      });
    }
  },
  methods: {
    //取消
    quxiao() {
      this.form = {
        subjectID: null, //学科
        catalogID: null, //目录
        enterpriseID: null, //企业
        province: null, //省份
        city: null, //城市
        direction: null, //方向
        questionType: "1", //题型
        difficulty: "1", //难度
        question: null, //题干
        videoURL: null, //解析视频
        answer: null, //答案解析
        remarks: null, //题目备注
        tags: null, //试题标签
        options: [] //选项
      };
    },
    //多选题
    duoxuan(item) {
      this.list.xxlist.forEach(ite => {
        ite.isRight = false;
        item.forEach(it => {
          if (ite == it) {
            it.isRight = true;
          }
        });
      });
    },
    //单选题
    danxuan(item) {
      this.list.xxlist = this.list.xxlist.map(ite => {
        if (ite == item) {
          ite.isRight = true;
        } else {
          ite.isRight = false;
        }
        return ite;
      });
    },
    //删除
    del(index) {
      this.list.xxlist.splice(index, 1);
    },
    //添加
    tianjia() {
      let key = this.list.js;
      let code = this.list.xuanx[this.list.xxlist.length];
      console.log(code);

      this.list.xxlist = this.list.xxlist.concat({
        key,
        code: null,
        isRight: false,
        title: ""
      });
      this.list.js += 1;
      console.log(this.list.xxlist);
    },
    //提交
    async tijiao() {
      this.$refs.myform.validate(async isok => {
        if (isok) {
          this.list.xxlist.forEach(item => {
            item.isRight = item.isRight ? 1 : 0;
          });
          switch (this.form.questionType) {
            case "1":
              this.form.options = this.list.xxlist;
              break;
            case "2":
              this.form.options = this.list.xxlist;
              break;
            case "3":
              break;
          }
          let resp = await que_add(this.form);
          console.log(resp);
        }
      });
    },
    //获取
    async getSome(method, attr, data = null) {
      let resp = await method();
      this.list[attr] = data ? resp.data[data] : resp.data;
      console.log(resp.data);
      console.log(this.list[attr]);
    }
  },
  created() {
    // this.getsubjects();
    //获取学科
    this.getSome(sub_simple, "subjects");
    //获取目录
    this.getSome(dir_simple, "directorys");
    //获取企业
    this.getSome(com_list, "companys", "items");
    //获取城市
    // console.log(provinces());
  },
  components: { quillEditor }
};
</script>

<style>
span {
}
</style>