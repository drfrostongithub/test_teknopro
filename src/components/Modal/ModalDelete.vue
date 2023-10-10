<template>
  <div v-if="show" class="modal">
    <div class="modal-content">
      <h2>Are you sure you want to delete?</h2>
      <div class="modal-buttons">
        <button @click="this.$emit('close')">No</button>
        <button @click="confirmDelete">Yes</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  props: ['show', 'id', 'type'],
  name: 'ModalDelete',
  methods: {
    async confirmDelete() {
      try {
        await axios.delete(`https://dummyapi.io/data/v1/${this.type}/${this.id}`, {
          headers: {
            'app-id': '62996cb2689bf0731cb00285' // Replace with your actual app ID
          }
        })
        this.$emit('notification-message', 'success', 'User deleted successfully!')
      } catch (error) {
        this.$emit('notification-message', 'fail', 'Failed to delete user!')
      }
      this.$emit('close')
    }
  }
}
</script>

<style>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
}

.modal-buttons {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}

.modal-buttons button {
  margin-left: 10px;
}
</style>
