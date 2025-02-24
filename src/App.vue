<script setup>
import { ref } from 'vue';
import HelloWorld from './components/HelloWorld.vue';
import Dialog from './Dialog.vue';
const show = ref(false);
function showPopup() {
  show.value = !show.value;
}
</script>

<template>
  <div>
    <button @click="showPopup">显示弹窗</button>
    <Dialog v-model:show="show"></Dialog>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
