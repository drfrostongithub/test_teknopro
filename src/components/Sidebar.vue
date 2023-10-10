<template>
  <div class="sidebar">
    <button @click="$emit('changePage', 'home')">Home</button>
    <button @click="$emit('changePage', 'user')">User</button>
    <button @click="$emit('changePage', 'post')">Post</button>
  </div>
</template>

<script>
export default {
  name: 'SideBar'
}
</script>

<style scoped>
.sidebar {
  width: 200px;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  background-color: #333;
  color: #fff;
  display: flex;
  flex-direction: column;
  padding: 20px;
  box-sizing: border-box;
}

.sidebar button {
  color: #fff;
  background-color: #666;
  border: none;
  padding: 10px;
  margin: 10px 0;
  cursor: pointer;
  text-align: left;
  width: 100%;
  box-sizing: border-box;
}

.sidebar button:hover {
  background-color: #888;
}
</style>
