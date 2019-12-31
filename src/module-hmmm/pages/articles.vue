<template>
  <div class="dashboard-container">
    <div class="app-container">
      面试技巧
    <el-button @click="$router.push({name:'articles-add'})">新增技巧</el-button>
    </div>
    <article-list></article-list>
  </div>
</template>

<script>
import article_list from '../components/dyh_articles'
export default {
  name: 'ArticlesList',
  data() {
    return {}
  },
  components:{
    'article-list':article_list
  }
}
</script>

<style scoped>
</style>
