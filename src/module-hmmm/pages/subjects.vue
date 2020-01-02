<template>
  <div class="dashboard-container">
    <el-card>
      <!--  -->
      <el-row slot="header">
        <el-button type="primary">新增学科</el-button>
      </el-row>
      <!--  -->
      <el-row>
        <el-form :inline="true" :label-position="right" label-width="80px">
          <el-form-item label="学科名称" >
            <el-input :autofocus='true'></el-input>
          </el-form-item>
          <el-button>清除</el-button>
          <el-button type="primary">搜索</el-button>
        </el-form>
      </el-row>
      <!--  -->
      <el-table>
        <el-table-column label="序号" prop=""></el-table-column>
        <el-table-column label="学科名称" prop=""></el-table-column>
        <el-table-column label="创建者" prop=""></el-table-column>
        <el-table-column label="创建日期" prop=""></el-table-column>
        <el-table-column label="前台是否显示" prop=""></el-table-column>
        <el-table-column label="二级目录" prop=""></el-table-column>
        <el-table-column label="标签" prop=""></el-table-column>
        <el-table-column label="题目数量" prop=""></el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-row type="flex" justify="end" style="margin-top:20px">
        <span style="height:35px;line-height:35px;font-size:8px">共{{count}}条</span>
        <el-pagination
          background
          layout="prev, pager, next"
          :page-size=page.pagesize
          :current-page=page.page
          :total=page.pages>
        </el-pagination>
      </el-row>
      
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'SubjectsList',
  data() {

    return {
      page:{
        pagesize:10,
        page:100,
        pages:0
      }
    }

  }
}
</script>

<style scoped>
</style>