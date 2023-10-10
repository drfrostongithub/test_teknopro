<template>
  <notification-message
    v-if="notificationMessage"
    :message="notificationMessage"
    :status="notificationStatus"
    @close="notificationMessage = ''"
  ></notification-message>
  <div class="container">
    <button @click="createUser()">Create User</button>
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Picture</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in paginatedUsers" :key="user.id">
          <td>{{ user.firstName }} {{ user.lastName }}</td>
          <td>
            <img :src="user.picture" alt="User Picture" @click="enlargeImage(user.picture)" />
          </td>
          <td>
            <button @click="editUser(user.id)">Edit</button>
            <button @click="deleteUser(user.id)">Delete</button>
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
        :disabled="currentPage === Math.ceil(users.length / itemsPerPage)"
      >
        Next
      </button>
    </div>
    <modal-image v-if="showImageModal" :imgSrc="currentImgSrc" @close="showImageModal = false" />
    <modal-edit-user
      :show="showEditModal"
      :id="userToEditId"
      @close="showEditModal = false"
      @notification-message="updateMessage"
    />
    <modal-delete
      :show="showDeleteModal"
      :id="userToDeleteId"
      :type="typeDeletion"
      @close="showDeleteModal = false"
      @notification-message="updateMessage"
    />
    <modal-new-user
      :show="showAddModal"
      @close="showAddModal = false"
      @notification-message="updateMessage"
    />
  </div>
</template>

<script>
import axios from 'axios'
import ModalImage from './Modal/ModalImage.vue'
import ModalEditUser from './Modal/ModalEditUser.vue'
import ModalNewUser from './Modal/ModalNewUser.vue'
import ModalDelete from './Modal/ModalDelete.vue'
import NotificationMessage from './Modal/NotificationMessage.vue'

export default {
  name: 'UserWeb',
  components: {
    ModalImage,
    ModalEditUser,
    ModalNewUser,
    ModalDelete,
    NotificationMessage
  },
  data() {
    return {
      users: [],
      editUsers: [],
      currentPage: 1,
      itemsPerPage: 6,
      showImageModal: false,
      currentImgSrc: '',
      userToEditId: null,
      userToDeleteId: null,
      showEditModal: false,
      showAddModal: false,
      showDeleteModal: false,
      notificationMessage: '',
      notificationStatus: '',
      typeDeletion: null
    }
  },
  computed: {
    paginatedUsers() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = start + this.itemsPerPage
      return this.users.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.users.length / 6)
    }
  },
  mounted() {
    this.fetchUser()
  },
  methods: {
    async fetchUser() {
      const response = await axios.get('https://dummyapi.io/data/v1/user?limit=50&page=1', {
        headers: {
          'app-id': '62996cb2689bf0731cb00285'
        }
      })
      this.users = response.data.data
    },
    numberOfPages() {
      return Math.ceil(this.users.length / this.itemsPerPage)
    },
    enlargeImage(imgSrc) {
      this.currentImgSrc = imgSrc
      this.showImageModal = true
    },
    editUser(userId) {
      this.userToEditId = userId
      this.showEditModal = true
    },
    createUser() {
      this.showAddModal = true
    },
    updateMessage(sts, msg) {
      if (sts === 'success') {
        this.fetchUser()
      }
      this.notificationStatus = sts
      this.notificationMessage = msg
    },
    deleteUser(userId) {
      this.userToDeleteId = userId
      this.showDeleteModal = true
      this.typeDeletion = 'user'
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
