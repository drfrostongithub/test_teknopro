<template>
  <div v-if="show" class="modal">
    <div class="modal-content">
      <span class="close" @click="this.$emit('close')">&times;</span>
      <h2>Create New User</h2>

      <label>Title:</label>
      <select v-model="user.title">
        <option value="mr">Mr</option>
        <option value="ms">Ms</option>
        <option value="mrs">Mrs</option>
      </select>

      <label>First Name:</label>
      <input v-model="user.firstName" type="text" />

      <label>Last Name:</label>
      <input v-model="user.lastName" type="text" />

      <label>Email:</label>
      <input v-model="user.email" type="email" />

      <label>Picture URL:</label>
      <input v-model="user.picture" type="text" />

      <div class="footer">
        <button @click="this.$emit('close')">Close</button>
        <button @click="save">Submit</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  props: ['show'],
  name: 'ModalEditUser',
  data() {
    return {
      user: {
        title: null,
        firstName: null,
        lastName: null,
        email: null,
        picture: null
      }
    }
  },
  methods: {
    async save() {
      try {
        await axios.post(`https://dummyapi.io/data/v1/user/create`, this.user, {
          headers: {
            'app-id': '62996cb2689bf0731cb00285' // Replace with your actual app ID
          }
        })
        this.$emit('notification-message', 'success', 'User created successfully!')
      } catch (error) {
        this.$emit('notification-message', 'fail', 'Failed to create user!')
      }
      this.$emit('close')
    }
  }
}
</script>

<style scoped>
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  display: flex;
  flex-direction: column;
  gap: 10px;
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.modal-content h2 {
  text-align: center;
}

.close {
  color: #aaa;
  float: left;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

.footer button {
  width: 50%;
}
</style>
