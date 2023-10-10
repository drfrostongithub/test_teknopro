<template>
  <div class="home-page">
    <input type="text" v-model="search" placeholder="Search by tag" />
    <div class="content">
      <div class="image-grid">
        <div class="image-box" v-for="post in paginatedPosts" :key="post.id">
          <img :src="post.image" :alt="post.text" onerror="this.onerror=null;" />
          <p>
            {{ post.owner.firstName }}
            {{ post.owner.lastName }}
          </p>
          <div class="tag-info">
            <p v-for="tag in post.tags" :key="tag" class="tag-link">#{{ tag }}</p>
          </div>
        </div>
      </div>
      <div class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1">Prev</button>
        <button
          v-for="page in totalPages"
          :key="page"
          :class="{ active: currentPage === page }"
          @click="changePage(page)"
        >
          {{ page }}
        </button>
        <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'PostWeb',
  data() {
    return {
      posts: [],
      search: '',
      currentPage: 1,
      postsPerPage: 8
    }
  },
  computed: {
    filteredPosts() {
      if (!this.search) {
        return this.posts
      }
      return this.posts.filter((post) => post.tags.some((tag) => tag.includes(this.search)))
    },
    paginatedPosts() {
      const start = (this.currentPage - 1) * this.postsPerPage
      const end = start + this.postsPerPage
      return this.filteredPosts.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.filteredPosts.length / this.postsPerPage)
    }
  },
  methods: {
    changePage(page) {
      this.currentPage = page
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++
      }
    }
  },
  async mounted() {
    const response = await axios.get('https://dummyapi.io/data/v1/post?limit=50&page=1', {
      headers: {
        'app-id': '62996cb2689bf0731cb00285'
      }
    })
    this.posts = response.data.data
  }
}
</script>

<style scoped>
.image-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  justify-items: center;
  margin: 0 auto;
  max-width: 800px;
}

.image-box {
  border: 1px solid #ccc;
  padding: 10px;
  box-sizing: border-box;
  text-align: center;
  background-color: #ccc;
}

.image-box img {
  width: 100%;
  height: 75%;
  object-fit: cover;
}

.tag-info {
  display: flex;
  margin-top: 5px;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  width: 100%;
  display: flex;
  justify-content: center;
  margin-top: 10%;
}

.tag-link {
  text-decoration: none;
  color: #2438ee;
  margin-right: 5px;
}

.tag-link:hover {
  color: #007bff;
}
</style>
