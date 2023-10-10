<template>
  <div id="app">
    <Sidebar @changePage="changePage" />
    <div class="main-content">
      <component :is="currentPage" />
    </div>
  </div>
</template>

<script>
import Sidebar from './components/Sidebar.vue'
import Home from './components/Home.vue'
import User from './components/User.vue'
import Post from './components/Post.vue'

export default {
  components: {
    Sidebar,
    Home,
    User,
    Post
  },
  data() {
    return {
      currentPage: 'Home'
    }
  },
  methods: {
    changePage(page) {
      this.currentPage = page.charAt(0).toUpperCase() + page.slice(1)
    }
  }
}
</script>

<style scoped>
#app {
  display: flex;
}

.main-content {
  margin-left: 200px;
  padding: 20px;
  width: calc(100% - 200px);
  box-sizing: border-box;
  overflow-y: auto;
  height: 100vh;
}
</style>
