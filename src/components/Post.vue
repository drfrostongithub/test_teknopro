<template>
  <notification-message
    v-if="notificationMessage"
    :message="notificationMessage"
    :status="notificationStatus"
    @close="notificationMessage = ''"
  ></notification-message>
  <div class="container">
    <button @click="createPost()">Create Post</button>
    <table>
      <thead>
        <tr>
          <th>Text</th>
          <th>Tags</th>
          <th>Image</th>
          <th>User</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in paginatedPosts" :key="post.id">
          <td>{{ post.text }}</td>
          <td>
            <p v-for="tag in post.tags" :key="tag" class="tag-link">#{{ tag }}</p>
          </td>
          <td><img :src="post.image" alt="Post Picture" @click="enlargeImage(post.image)" /></td>
          <td>{{ post.owner.firstName }} {{ post.owner.lastName }}</td>
          <td>
            <button @click="editPost(post.id)">Edit</button>
            <button @click="deletePost(post.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <div>
      <button @click="currentPage--" :disabled="currentPage === 1">Previous</button>
      <button
        v-for="page in totalPages"
        :key="page"
        :class="{ active: currentPage === page }"
        @click="changePage(page)"
      >
        {{ page }}
      </button>
      <button
        @click="currentPage++"
        :disabled="currentPage === Math.ceil(posts.length / itemsPerPage)"
      >
        Next
      </button>
    </div>
    <modal-image v-if="showModalImage" :imgSrc="currentImgSrc" @close="showModalImage = false" />
    <modal-delete
      :show="showDeleteModal"
      :id="postToDeleteId"
      :type="typeDeletion"
      @close="showDeleteModal = false"
      @notification-message="updateMessage"
    />
    <modal-edit-post
      :show="showEditModal"
      :id="postToEditId"
      @close="showEditModal = false"
      @notification-message="updateMessage"
    />
    <modal-new-post
      :show="showAddModal"
      @close="showAddModal = false"
      @notification-message="updateMessage"
    />
  </div>
</template>

<script>
import axios from 'axios'
import ModalImage from './Modal/ModalImage.vue'
import ModalDelete from './Modal/ModalDelete.vue'
import NotificationMessage from './Modal/NotificationMessage.vue'
import ModalEditPost from './Modal/ModalEditPost.vue'
import ModalNewPost from './Modal/ModalNewPost.vue'

export default {
  name: 'PostWeb',
  components: {
    ModalImage,
    ModalDelete,
    ModalEditPost,
    ModalNewPost,
    NotificationMessage
  },
  data() {
    return {
      posts: [],
      currentPage: 1,
      itemsPerPage: 6,
      showModalImage: false,
      currentImgSrc: '',
      showDeleteModal: false,
      showEditModal: false,
      showAddModal: false,
      postToDeleteId: null,
      postToEditId: null,
      typeDeletion: null,
      notificationMessage: '',
      notificationStatus: ''
    }
  },
  computed: {
    paginatedPosts() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = start + this.itemsPerPage
      return this.posts.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.posts.length / 6)
    }
  },
  async mounted() {
    this.fetchPost()
  },
  methods: {
    async fetchPost() {
      const response = await axios.get('https://dummyapi.io/data/v1/post?limit=50&page=1', {
        headers: {
          'app-id': '62996cb2689bf0731cb00285'
        }
      })
      this.posts = response.data.data
    },
    numberOfPages() {
      return Math.ceil(this.posts.length / this.itemsPerPage)
    },
    enlargeImage(imgSrc) {
      this.currentImgSrc = imgSrc
      this.showModalImage = true
    },
    updateMessage(sts, msg) {
      if (sts === 'success') {
        this.fetchPost()
      }
      this.notificationStatus = sts
      this.notificationMessage = msg
    },
    deletePost(postId) {
      this.postToDeleteId = postId
      this.showDeleteModal = true
      this.typeDeletion = 'post'
    },
    editPost(postId) {
      this.postToEditId = postId
      this.showEditModal = true
    },
    createPost() {
      this.showAddModal = true
    },
    changePage(page) {
      this.currentPage = page
    }
  }
}
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  flex-direction: column;
}

table {
  border-collapse: collapse;
  width: 400px;
  border: 1px solid #ccc;
}

th,
td {
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}

tr:nth-child(even) {
  background-color: #f9f9f9;
}

tr:hover {
  background-color: #e9e9e9;
}

img {
  width: 50px;
  height: 50px;
  object-fit: cover;
}
</style>
