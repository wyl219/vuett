<!-- src/components/SearchBar.vue -->
<template>
  <div class="search-bar">
    <input v-model="query" @input="onInput" placeholder="百度一下，你就知道">
    <button @click="search">百度一下</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: ''
    };
  },
  methods: {
    onInput() {
      // 处理输入事件
    },
    search() {
      // 处理搜索事件
      this.$emit('search', this.query);
    }
  }
};
</script>

<style scoped>
/* 样式可以根据实际需要进行调整 */
.search-bar {
  display: flex;
  align-items: center;
}
input {
  flex: 1;
  padding: 10px;
}
button {
  padding: 10px;
  background-color: #3385ff;
  color: #fff;
  border: none;
  cursor: pointer;
}
</style>
