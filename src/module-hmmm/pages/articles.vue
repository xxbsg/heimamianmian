<template>
  <!-- <div class="dashboard-container">
    
    
  </div> -->
  <article-list></article-list>
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
