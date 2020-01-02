<template>
<el-card>
  <el-table :data="list" border>
    <el-table-column label="序号" prop="id"></el-table-column>
    <el-table-column label="组题时间" prop="addTime">
       <template slot-scope="obj">{{obj.row.addTime | parseTimeByString}}</template>
    </el-table-column>
    <el-table-column label="用户名" prop="userName">
       <template slot-scope="obj">{{obj.row.userName}}</template>
    </el-table-column>
    <el-table-column label="试题ID" prop="questionIDs"></el-table-column>
    <el-table-column label="答题进度" prop="progressOfAnswer">
       <template slot-scope="obj">{{obj.row.progressOfAnswer + '%'}}</template>
    </el-table-column>
    <el-table-column label="正确率" prop="accuracyRate">
       <template slot-scope="obj">{{obj.row.accuracyRate + '%'}}</template>
    </el-table-column>
    <el-table-column label="答题总耗时(秒)" prop="totalSeconds"></el-table-column>
    <el-table-column label="组题类型/详情" prop="questionType"></el-table-column>
    <el-table-column label="操作">
      <template slot-scope="obj">
          <el-button @click="delItem(obj.row.id)" type="text" size="small">删除</el-button>
        </template>
    </el-table-column>
  </el-table>
  </el-card>
</template>

<script>
import { randoms, removeRandoms } from '../../api/hmmm/questions'
  export default {
  name: 'QuestionsRandoms',
  data(){
    return {
      list:[],
      users: []
    }
  },
  methods:{
    // 删除
    async delItem(id) {
      await this.$confirm("是否确认删除此数据");
      await removeRandoms({ id });
      this.$message({ type: "success", message: "删除成功" });
      this.getCondition();
    },
    questionFormatter(row, column, cellValue) {
      let result = this.questionType.filter(item => item.value == cellValue);
      return result.length ? result[0].label : "未知";
    },
    // 获取数据
    async getQuestion(data) {
      let result = await randoms(data);
      this.list = result.data.items;
    }
  },
  created() {
    this.getQuestion();
  }
}
</script>

<style lang="less" scoped>

</style>