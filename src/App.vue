<template>
  <div id="app-container">
    <ExhibitionPage />
  </div>
</template>

<script setup>
import ExhibitionPage from './components/ExhibitionPage.vue'; // 确保路径正确
</script>

<style>
/* 全局样式，确保页面基本重置 */
body, html {
  margin: 0;
  padding: 0;
  font-family: sans-serif; /* 设置一个基础字体 */
  background-color: #E9E9E9; /* 可以设置一个与第二屏匹配的全局背景色 */
  scroll-behavior: smooth; /* 平滑滚动效果 */
}

#app-container {
  /* 如果需要，可以在这里添加针对整个应用的样式 */
}
</style>