<template>
  <div v-if="show && post && users" class="modal">
    <div class="modal-content">
      <span class="close" @click="this.$emit('close')">&times;</span>
      <h2>Edit Post</h2>
      <label for="owner">Owner:</label>
      <select disabled id="owner" v-model="selectedOwner">
        <option v-for="user in users" :value="user.id" :key="user.id">
          {{ user.firstName }} {{ user.lastName }}
        </option>
      </select>
      <br />
      <label for="text">Text:</label>
      <input type="text" id="text" v-model="post.text" />
      <br />
      <label for="image">Image:</label>
      <input type="text" id="image" v-model="post.image" />
      <br />
      <label for="tags">Tags:</label>
      <input type="text" id="tags" v-model="tagInput" @keydown.enter.prevent="addTag" />
      <button type="button" @click="addTag">Add Tag</button>
      <div>
        <p v-for="(tag, index) in post.tags" :key="index" class="tag-link">
          #{{ tag }} <button type="button" @click="removeTag(index)">x</button>
        </p>
      </div>

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
  props: ['show', 'id'],
  name: 'ModalEditPost',
  data() {
    return {
      post: {
        text: '',
        image: '',
        tags: []
      },
      users: [],
      selectedOwner: '',
      tagInput: ''
    }
  },
  mounted() {
    this.fetchUsers()
  },
  watch: {
    id: {
      handler: function (newId) {
        if (newId) {
          this.fetchPost(newId)
        }
      },
      immediate: true
    }
  },
  methods: {
    addTag() {
      if (this.tagInput.trim() !== '') {
        this.post.tags.push(this.tagInput.trim())
        this.tagInput = ''
      }
    },
    removeTag(index) {
      this.post.tags.splice(index, 1)
    },
    async save() {
      try {
        await axios.put(`https://dummyapi.io/data/v1/post/${this.post.id}`, this.post, {
          headers: {
            'app-id': '62996cb2689bf0731cb00285'
          }
        })
        this.$emit('notification-message', 'success', 'User updated successfully!')
      } catch (error) {
        this.$emit('notification-message', 'fail', 'Failed to update user!')
      }
      this.$emit('close')
    },
    async fetchUsers() {
      await axios
        .get('https://dummyapi.io/data/v1/user/', {
          headers: {
            'app-id': '62996cb2689bf0731cb00285'
          }
        })
        .then((response) => {
          this.users = response.data.data
        })
        .catch((error) => {
          console.error(error)
        })
    },
    async fetchPost(id) {
      await axios
        .get(`https://dummyapi.io/data/v1/post/${id}`, {
          headers: {
            'app-id': '62996cb2689bf0731cb00285'
          }
        })
        .then((response) => {
          this.post = response.data
          this.selectedOwner = response.data.owner.id
        })
        .catch((error) => {
          console.error(error)
        })
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
