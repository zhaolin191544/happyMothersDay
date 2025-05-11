<template>
  <div class="person-card">
    <div class="avatar-container">
      <img :src="person.avatar" :alt="person.name" class="avatar-img" />
    </div>
    <h3 class="person-name">{{ person.name }}</h3>
    <p class="person-description" v-html="formatDescription(person.description)"></p>
    <p class="person-website" v-if="person.websiteLink && person.websiteText">
      个人网站：<a :href="person.websiteLink" target="_blank" rel="noopener noreferrer">{{ person.websiteText }}</a>
    </p>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  person: {
    type: Object,
    required: true,
    default: () => ({
      avatar: '',
      name: '姓名',
      description: '个人描述文本...',
      websiteLink: '#',
      websiteText: 'example.com'
    })
  }
});

// 格式化描述文本，将换行符转为<br>，如果需要的话
// 这里简单实现，实际中可能需要更复杂的HTML处理或Markdown解析
function formatDescription(description) {
  return description.replace(/\n/g, '<br />');
}
</script>

<style scoped>
.person-card {
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08); /* 更柔和的阴影 */
  padding: 30px 35px;
  text-align: center;
  width: 100%;
  max-width: 460px; /* 根据图片中卡片比例调整 */
  /* margin: 20px;  <-- 这个 margin 由父组件的 gap 控制 */
  color: #333;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center; /* 确保内容在卡片内居中 */
}

.avatar-container {
  width: 110px; /* 根据图片调整 */
  height: 110px;
  border-radius: 50%;
  overflow: hidden;
  margin: 0 auto 25px auto;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  flex-shrink: 0; /* 防止头像被压缩 */
}

.avatar-img {
  width: 100%;
  height: 100%;
  object-fit: cover; /* 保证图片填满并保持比例 */
}

.person-name {
  font-size: 1.7rem; /* 调整字号 */
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 18px; /* 调整间距 */
}

.person-description {
  font-size: 0.9rem;
  line-height: 1.65; /* 调整行高 */
  color: #4a4a4a; /* 描述文字颜色稍浅 */
  text-align: left; /* 描述文本左对齐 */
  margin-bottom: 25px; /* 调整间距 */
  width: 100%; /* 确保描述占据整个宽度 */
}

.person-website {
  font-size: 0.9rem;
  color: #333;
  margin-top: auto; /* 如果卡片高度不一致，将网站链接推到底部 */
}

.person-website a {
  color: #c0392b; /* 图片中链接的红色调 */
  text-decoration: none;
  font-weight: 500;
}

.person-website a:hover {
  text-decoration: underline;
  color: #e74c3c;
}

/* 响应式调整卡片内部 */
@media (max-width: 768px) {
  .person-card {
    max-width: 90%; /* 在小屏幕上，卡片宽度可以更宽 */
    padding: 25px 30px;
  }
  .avatar-container {
    width: 100px;
    height: 100px;
    margin-bottom: 20px;
  }
  .person-name {
    font-size: 1.5rem;
  }
  .person-description {
    font-size: 0.85rem;
  }
}
</style>