<template>
  <div :class="`notification ${status}`">
    <span @click="this.$emit('close')">X</span>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  props: {
    message: {
      type: String,
      required: true
    },
    status: {
      type: String,
      required: true
    }
  }
}
</script>

<style>
.notification {
  position: fixed;
  top: 5%;
  right: 5%;
  padding: 20px;
  color: white;
  transition: all 0.5s ease;
}

.notification.success {
  background-color: green;
}

.notification.fail {
  background-color: red;
}

.notification span {
  float: right;
  cursor: pointer;
}
</style>
