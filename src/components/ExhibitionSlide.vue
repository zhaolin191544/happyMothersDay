// src/components/ExhibitionSlide.vue
<template>
  <div class="exhibition-slide" :class="{ active: isActive }">
    <div class="slide-content">
      <div class="image-container" :class="{ 'visible': imageVisible }">
        <img v-if="slide.image" :src="slide.image" :alt="slide.title" />
      </div>
      <div class="text-container">
        <h3 class="slide-title" :class="{ 'visible': titleVisible }">{{ slide.title }}</h3>
        <p class="slide-description" :class="{ 'visible': descriptionVisible }" v-html="formatText(slide.description)"></p>
        <div class="slide-meta" :class="{ 'visible': metaVisible }">
          <span class="year">{{ slide.year }}</span>
          <span class="type">{{ slide.type }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, toRefs } from 'vue';

const props = defineProps({
  slide: {
    type: Object,
    required: true
  },
  isActive: { // 当前幻灯片是否为活动状态
    type: Boolean,
    default: false
  },
  currentStep: { // 当前活动幻灯片内部的显示步骤
    type: Number,
    default: 0
  }
});

const { isActive, currentStep } = toRefs(props);

const imageVisible = ref(false);
const titleVisible = ref(false);
const descriptionVisible = ref(false);
const metaVisible = ref(false);

// 监听 isActive 和 currentStep 来控制元素的逐步显示
watch([isActive, currentStep], ([active, step]) => {
  if (active) {
    // 根据步骤逐步显示元素，可以根据需求调整延迟和顺序
    imageVisible.value = step >= 0; // 图片通常首先或与标题一起显示
    titleVisible.value = step >= 0;
    descriptionVisible.value = step >= 1;
    metaVisible.value = step >= 2;
  } else {
    // 非活动幻灯片则隐藏所有元素，为下次进入做准备
    imageVisible.value = false;
    titleVisible.value = false;
    descriptionVisible.value = false;
    metaVisible.value = false;
  }
}, { immediate: true });


function formatText(text) {
  return text ? text.replace(/\n/g, '<br />') : '';
}
</script>

<style scoped>
.exhibition-slide {
  width: 100%;
  height: 100%; /* 占据父容器（通常是视口高度）*/
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute; /* 用于幻灯片切换时的层叠 */
  top: 0;
  left: 0;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.7s ease-in-out, visibility 0s linear 0.7s;
  background-color: #000; /* 背景为黑色 */
  color: #fff;
  padding: 20px; /* 增加一些内边距 */
  box-sizing: border-box;
}

.exhibition-slide.active {
  opacity: 1;
  visibility: visible;
  transition: opacity 0.7s ease-in-out, visibility 0s linear 0s;
  z-index: 10; /* 活动幻灯片在最上层 */
}

.slide-content {
  display: flex;
  align-items: center; /* 垂直居中内容 */
  justify-content: space-around; /* 图片和文字左右分布 */
  width: 100%;
  max-width: 1200px; /* 限制内容最大宽度 */
  gap: 40px; /* 图片和文字之间的间隙 */
}

.image-container {
  flex: 0 0 55%; /* 图片区域占据约一半宽度，可调整 */
  max-width: 55%;
  opacity: 0;
  transform: translateX(-30px);
  transition: opacity 0.8s ease-out 0.2s, transform 0.8s ease-out 0.2s; /* 延迟出现 */
}
.image-container.visible {
  opacity: 1;
  transform: translateX(0);
}
.image-container img {
  max-width: 100%;
  max-height: 70vh; /* 限制图片最大高度 */
  display: block;
  object-fit: contain; /* 保持图片比例 */
  border: 1px solid #333; /* 图片边框，根据图片效果添加 */
}

.text-container {
  flex: 0 0 40%; /* 文字区域占据约一半宽度，可调整 */
  max-width: 40%;
  display: flex;
  flex-direction: column;
  align-items: flex-start; /* 文字左对齐 */
  font-family: 'Noto Serif SC', 'Songti SC', serif; /* 宋体或类似衬线字体 */
}

/* 通用元素入场动画 */
.slide-title, .slide-description, .slide-meta {
  opacity: 0;
  transform: translateY(20px);
  transition-property: opacity, transform;
  transition-duration: 0.6s;
  transition-timing-function: ease-out;
}
.slide-title.visible { opacity: 1; transform: translateY(0); transition-delay: 0.4s; }
.slide-description.visible { opacity: 1; transform: translateY(0); transition-delay: 0.6s; }
.slide-meta.visible { opacity: 1; transform: translateY(0); transition-delay: 0.8s; }


.slide-title {
  font-size: 1.8rem; /* 根据图片调整 */
  font-weight: bold;
  margin-bottom: 20px;
  line-height: 1.4;
}

.slide-description {
  font-size: 1rem; /* 根据图片调整 */
  line-height: 1.8;
  margin-bottom: 30px;
  color: #e0e0e0; /* 描述文字稍暗 */
  white-space: pre-line; /* 保留换行符效果 */
}

.slide-meta {
  font-size: 0.9rem;
  color: #aaa;
}
.slide-meta .year {
  margin-right: 15px;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .slide-content {
    flex-direction: column; /* 小屏幕上图片和文字垂直排列 */
    gap: 30px;
    text-align: center; /* 文字居中 */
    overflow-y: auto; /* 如果内容过多，允许滚动 */
    height: calc(100% - 80px); /* 减去顶部UI空间 */
  }
  .image-container, .text-container {
    flex-basis: auto;
    max-width: 90%; /* 允许更宽 */
  }
  .text-container {
    align-items: center;
  }
  .slide-title {
    font-size: 1.5rem;
  }
  .slide-description {
    font-size: 0.9rem;
  }
}
</style>