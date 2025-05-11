<template>
  <div class="page-container" :class="{ 'no-scroll': isExhibitionActive }">
    <section class="screen-section hero-section" id="hero" ref="heroSectionRef">
      <div class="content-center" style="margin-top: -20px;">
        <h1 class="title">
          <span class="typed-text">{{ displayText }}</span>
          <span class="cursor" :class="{ 'is-typing': isTyping }"></span>
        </h1>
        <h2 class="subtitle">A Digital Exhibition</h2>
        <div class="details">
          <p class="date">2025.05.11</p>
          <p class="venue">敬天底下所有伟大的母亲</p>
          <p class="address">coded in ShenZhen University</p>
        </div>
      </div>
      <div class="scroll-prompt">
        <p>滑动或鼠标滚轮观看展览</p>
        <div class="arrow-down"></div>
      </div>
    </section>

    <section
      class="screen-section exhibition-viewer-section"
      id="exhibition-viewer"
      ref="exhibitionViewerSectionRef"
      :class="{ 'activated': isExhibitionActive, 'visible': initialExhibitionPromptVisible || isExhibitionActive }"
    >
      <div
        class="exhibition-entry-card-wrapper"
        v-if="!isExhibitionActive && initialExhibitionPromptVisible"
        ref="exhibitionEntryCardRef"
      >
        <div class="exhibition-entry-card">
          <div class="exhibition-entry-prompt">
            <div class="entry-card-top-bar">
                <span class="entry-card-counter">1/24</span>
                <span class="entry-card-title-center">母亲节快乐！</span>
                <span class="entry-card-top-right-text">感谢您的付出</span>
            </div>
            <h2>Happy Mother's Day!</h2>
            <div class="entry-card-bottom-bar"> 
                <button @click="enterExhibitionMode" class="enter-arrow-button">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" class="arrow-icon">
                        <path d="M438.6 278.6c12.5-12.5 12.5-32.8 0-45.3l-160-160c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3L338.8 224 32 224c-17.7 0-32 14.3-32 32s14.3 32 32 32l306.7 0L233.4 393.4c-12.5 12.5-12.5 32.8 0 45.3s32.8 12.5 45.3 0l160-160z"/>
                    </svg>
                </button>
            </div>
          </div>
        </div>
      </div>


      <div v-if="isExhibitionActive" class="exhibition-content-wrapper">
        <div class="exhibition-ui-overlay">
          <div class="top-bar">
            <span class="slide-counter">{{ currentSlideIndex + 1 }}/{{ exhibitionSlides.length }}</span>
            <span class="top-title">母亲节快乐！</span>
            <button @click="exitExhibitionMode()" class="exit-button"> 
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="18" height="18"><path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"/></svg>
              退出
            </button>
          </div>
          <div class="slides-container">
            <ExhibitionSlide
              v-for="(slide, index) in exhibitionSlides"
              :key="slide.id"
              :slide="slide"
              :is-active="index === currentSlideIndex"
              :current-step="currentSlideStep"
            />
          </div>
          <div class="bottom-bar">
            <span class="bottom-extra-text">感谢您的付出</span>
            <button @click="restartExhibition" class="restart-button">
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="18" height="18"><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10c2.756 0 5.278-.993 7.219-2.639.209-.173.418-.346.621-.525l-1.69-1.691c-.176.152-.352.304-.527.455C16.13 18.999 14.16 19.999 12 19.999c-4.411 0-8-3.589-8-8s3.589-8 8-8c2.783 0 5.221.925 7.104 2.738l-2.142 2.142H22V3l-2.447 2.447C17.339 3.208 14.792 2 12 2z"/></svg>
              RESTART
            </button>
          </div>
        </div>
      </div>
    </section>

    <div class="flower-container" style="width: 100%; background-color: #FEFEFE; display: flex; justify-content: center; align-items: center;">
      <img src="/flower.png" alt="Flower" style="max-width: 50%; height: auto;" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, nextTick,watch } from 'vue';
import ExhibitionSlide from './ExhibitionSlide.vue'; // 假设 ExhibitionSlide.vue 存在

// --- 打字机效果 (代码不变) ---
const textsToType = ["Happy Mother's Day"];
const typingSpeed = 150;
const erasingSpeed = 100;
const delayBeforeErase = 2000;
const delayBeforeNext = 500;
const displayText = ref('');
const currentTextIndex = ref(0);
const charIndex = ref(0);
const isDeleting = ref(false);
const isTyping = ref(true);
let typingTimeout;
let erasingTimeout;
let nextTextTimeout;
function typeEffect() {
  const currentText = textsToType[currentTextIndex.value];
  isTyping.value = true;
    if (!isDeleting.value && charIndex.value < currentText.length) {
    displayText.value += currentText.charAt(charIndex.value);
    charIndex.value++;
    typingTimeout = setTimeout(typeEffect, typingSpeed);
  } else if (!isDeleting.value && charIndex.value === currentText.length) {
    isTyping.value = false;
    erasingTimeout = setTimeout(() => {
      isDeleting.value = true;
      isTyping.value = true;
      typeEffect();
    }, delayBeforeErase);
  } else if (isDeleting.value && charIndex.value > 0) {
    displayText.value = currentText.substring(0, charIndex.value - 1);
    charIndex.value--;
    typingTimeout = setTimeout(typeEffect, erasingSpeed);
  } else if (isDeleting.value && charIndex.value === 0) {
    isDeleting.value = false;
    isTyping.value = false;
    currentTextIndex.value = (currentTextIndex.value + 1) % textsToType.length;
    nextTextTimeout = setTimeout(() => {
        isTyping.value = true;
        typeEffect();
    }, delayBeforeNext);
  }
}

const heroSectionRef = ref(null);
const creatorsSectionRef = ref(null);
const showCreators = ref(false);
let creatorsObserver;

// --- 展览幻灯片数据 (代码不变) ---
const exhibitionSlides = ref([
   { id: 1, image: './2.jpg', title: '常州恐龙园', description: '小时候的生活并不幸福\n但依然存在着美好的回忆', year: '2010', type: '旅行' },
   { id: 2, image: './3.jpg', title: '吃肯德基', description: '某一年的六一和母亲在外面时的回忆', year: '2009', type: '旅行' },
   { id: 3, image: './4.jpg', title: '常州恐龙园', description: '和母亲的合照\n也是我印象中的第一次一家三口出游', year: '2010', type: '旅行' },
   { id: 4, image: './5.jpg', title: '苏州水上乐园', description: '11年在苏州时的照片\n当时你和老爸看起来还很年轻', year: '2011', type: '旅行' },
   { id: 12, image: './13.jpg', title: '上海之行', description: '和你的表弟一家一起去的上海\n照片中的有几位现在已经不在了', year: '2011', type: '旅行' },
   { id: 13, image: './14.jpg', title: '黄山之行', description: '我读二年级时在黄山的照片\n当时你和老爸都正值壮年\n印象中当时的暑假你们都非常辛苦', year: '2012', type: '旅行' },
   { id: 5, image: './6.jpg', title: '无锡之行', description: '在无锡旅游时的照片\n当时已经有了小妹妹\n有趣的是那次旅行还有一位故人', year: '2016', type: '旅行' },
   { id: 6, image: './7.jpg', title: '南京', description: '2018年在南京\n妹妹已经相对较大了\n当时我在南京弄丢了我的身份证', year: '2018', type: '旅行' },
   { id: 7, image: './8.jpg', title: '三亚之行', description: '那年我中考结束\n应该是到目前为止我们出过的最远的一次门', year: '2019', type: '旅行' },
   { id: 8, image: './9.jpg', title: '嘉兴', description: '你和妹妹去的嘉兴\n我记得你发了个朋友圈\n说自己第二次当妈妈的感觉', year: '2024', type: '旅行' },
   { id: 9, image: './10.jpg', title: '部分全家福', description: '赵磊高考完的部分全家福\n可惜我当年高考完了冷冷清清', year: '2024', type: '全家福' },
   { id: 10, image: './11.jpg', title: '青岛之行', description: '摄于青岛栈桥\n由我全程安排的一次四人出行', year: '2024', type: '旅行' },
   { id: 11, image: './12.jpg', title: '深圳之行', description: '23年暑假你和老爸还有妹妹\n第一次来我上学的城市\n以后我也很可能要留在那边了', year: '2023', type: '旅行' },
   { id: 14, title: '结语', description: '今年母亲节\n突发奇想想要做一个母亲节礼物\n但是，翻看了很多次的家庭活动记录\n发现我的妈妈一直都不是个主角\n我想起来了我妈的微信签名\n"家有儿女，真心幸福"\n也许，孩子还有家人也同样能够让你感到幸福', type: '结语' },
   { id: 14, title: '结语', description: '那就祝您母亲节快乐吧！\n于我而言，你是个优秀的母亲\n没有你就没有我现在的生活\n其他话我也说不出口了\n等我慢慢完成自己的目标吧！', type: '结语' } 
]);


const exhibitionViewerSectionRef = ref(null);
const exhibitionEntryCardRef = ref(null); // 新增 ref 用于观察黑色圆角矩形
const isExhibitionActive = ref(false);
const initialExhibitionPromptVisible = ref(false); // 这个变量现在控制整个第三屏的初始可见性
const currentSlideIndex = ref(0);
const currentSlideStep = ref(0);
const maxStepsPerSlide = 3;

let exhibitionPromptObserver; // 用于观察黑色圆角矩形
let exhibitionSectionOverallObserver; // 用于观察整个第三屏区域
let wheelTimeout;

function enterExhibitionMode() {
  if (isExhibitionActive.value) return;
  isExhibitionActive.value = true;
  window.addEventListener('wheel', handleExhibitionScroll, { passive: false });
  currentSlideIndex.value = 0;
  currentSlideStep.value = 0;
}

function exitExhibitionMode(scrollToCreators = false) {
  if (!isExhibitionActive.value) return;
  isExhibitionActive.value = false;
  window.removeEventListener('wheel', handleExhibitionScroll);
  if (scrollToCreators && creatorsSectionRef.value) {
    nextTick(() => {
      creatorsSectionRef.value.scrollIntoView({ behavior: 'smooth' });
    });
  }
}

function restartExhibition() { /* ... (代码不变) ... */
    if (currentSlideIndex.value === 0 && currentSlideStep.value === 0) return;
    currentSlideIndex.value = 0;
    currentSlideStep.value = 0;
}
function handleExhibitionScroll(event) { /* ... (代码不变) ... */
  event.preventDefault();
  clearTimeout(wheelTimeout);
  wheelTimeout = setTimeout(() => {
    const deltaY = event.deltaY;
    if (deltaY > 0) {
      if (currentSlideStep.value < maxStepsPerSlide - 1) {
        currentSlideStep.value++;
      } else if (currentSlideIndex.value < exhibitionSlides.value.length - 1) {
        currentSlideIndex.value++;
        currentSlideStep.value = 0;
      }
    } else if (deltaY < 0) {
      if (currentSlideStep.value > 0) {
        currentSlideStep.value--;
      } else if (currentSlideIndex.value > 0) {
        currentSlideIndex.value--;
        currentSlideStep.value = maxStepsPerSlide - 1;
      } else if (currentSlideIndex.value === 0 && currentSlideStep.value === 0) {
        exitExhibitionMode(true);
      }
    }
  }, 70);
}

onMounted(() => {
  if (textsToType.length > 0) typeEffect();

  if (creatorsSectionRef.value) {
    creatorsObserver = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          showCreators.value = true;
        }
        // else { showCreators.value = false; } // 根据需要决定是否在离开时隐藏
      });
    }, { threshold: 0.1 });
    creatorsObserver.observe(creatorsSectionRef.value);
  }

  // 观察整个第三屏区域，使其在接近视口时可见
  if (exhibitionViewerSectionRef.value) {
    exhibitionSectionOverallObserver = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (!isExhibitionActive.value) {
                initialExhibitionPromptVisible.value = entry.isIntersecting;
            }
        });
    }, { threshold: 0.05 }); // 5% 可见时就让第三屏区域准备好
    exhibitionSectionOverallObserver.observe(exhibitionViewerSectionRef.value);
  }

  // 观察黑色圆角矩形展览入口卡片，用于自动进入展览
  if (exhibitionEntryCardRef.value) {
    exhibitionPromptObserver = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        // 当卡片大部分可见 (例如 75% intersectionRatio) 且接近屏幕中心时，自动进入
        // “接近屏幕中心”的判断可以简化为 intersectionRatio 达到一个较高值
        if (entry.isIntersecting && entry.intersectionRatio >= 0.75 && !isExhibitionActive.value) {
          enterExhibitionMode();
        }
      });
    }, { threshold: [0.75, 0.8, 0.85, 0.9, 0.95, 1.0] }); // 设置多个阈值以更精确地捕捉“居中”
    // 延迟观察，确保元素已渲染并且 initialExhibitionPromptVisible 为 true
    nextTick(() => {
        if (exhibitionEntryCardRef.value && initialExhibitionPromptVisible.value) {
            exhibitionPromptObserver.observe(exhibitionEntryCardRef.value);
        }
    });
  }
  // 如果 initialExhibitionPromptVisible 状态变化后 exhibitionEntryCardRef 变为可用，也需要启动观察
  const unwatch = watch(initialExhibitionPromptVisible, (newValue) => {
      if (newValue && exhibitionEntryCardRef.value && exhibitionPromptObserver) {
          exhibitionPromptObserver.observe(exhibitionEntryCardRef.value);
          unwatch(); // 观察到后即可停止 watch
      }
  });


});

onUnmounted(() => {
  clearTimeout(typingTimeout); clearTimeout(erasingTimeout); clearTimeout(nextTextTimeout);
  if (creatorsObserver && creatorsSectionRef.value) creatorsObserver.unobserve(creatorsSectionRef.value);
  if (exhibitionSectionOverallObserver && exhibitionViewerSectionRef.value) exhibitionSectionOverallObserver.unobserve(exhibitionViewerSectionRef.value);
  if (exhibitionPromptObserver && exhibitionEntryCardRef.value) exhibitionPromptObserver.unobserve(exhibitionEntryCardRef.value);
  window.removeEventListener('wheel', handleExhibitionScroll);
});

</script>

<style scoped>
.page-container.no-scroll {
  overflow: hidden;
}

.screen-section {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px 20px;
  box-sizing: border-box;
  position: relative;
  width: 100%;
}

/* --- 之前的 hero-section, creators-section 样式保持不变 --- */
.hero-section { /* ... (保持不变) ... */ background-color: #FEFEFE; font-family: 'Georgia', 'Times New Roman', Times, serif; color: #333; z-index: 1; }
.content-center { /* ... (保持不变) ... */ flex-grow: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; width: 100%; max-width: 900px; margin: 0 auto; }
.title { /* ... (保持不变) ... */ font-size: 4.8rem; font-weight: 600; color: #282c34; margin-bottom: 15px; min-height: 65px; letter-spacing: 0.03em; position: relative; }

.cursor { /* ... */ display: inline-block; width: 3px; height: 4.2rem; background-color: #282c34; margin-left: 8px; animation: blink 1s steps(1) infinite; vertical-align: bottom; }


@keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0; } }
.subtitle { /* ... */ font-size: 3rem; font-weight: 300; color: #4a4a4a; margin-top: 0; margin-bottom: 50px; letter-spacing: 0.02em; }
.details { /* ... */ font-size: 2rem; color: #555; line-height: 1.7; text-align: center; }
.details p { margin: 6px 0; }
.date { font-size: 2rem; color: #404040; }
.venue { font-size: 2rem; font-weight: 500; color: #333; }
.address { font-size: 2rem; color: #606060; }
.scroll-prompt { /* ... */ position: absolute; bottom: 40px; left: 50%; transform: translateX(-50%); display: flex; flex-direction: column; align-items: center; color: #707070; font-size: 1.6rem; animation: subtle-bounce 1s infinite ease-in-out; }
.scroll-prompt p { margin-bottom: 12px; }
.arrow-down { /* ... */ width: 14px; height: 14px; border-left: 2.5px solid #707070; border-bottom: 2.5px solid #707070; transform: rotate(-45deg); }
@keyframes subtle-bounce { 0%, 100% { transform: translate(-50%, 0); } 50% { transform: translate(-50%, -8px); } }

.creators-section {
  background-color: #FEFEFE;
  z-index: 1;
}
.creators-grid { /* ... */ display: flex; flex-wrap: wrap; justify-content: center; align-items: flex-start; gap: 40px; width: 100%; max-width: 1100px; padding: 20px 0; }
.card-container-wrapper { display: contents; }
.person-card-item { flex-shrink: 0; }
.fade-up-enter-active, .fade-up-leave-active { /* ... */ transition: opacity 0.7s cubic-bezier(0.25, 0.8, 0.25, 1), transform 0.7s cubic-bezier(0.25, 0.8, 0.25, 1); }
.fade-up-leave-active { transition-delay: 0s !important; }
.fade-up-enter-from, .fade-up-leave-to { opacity: 0; transform: translateY(40px); }
.fade-up-enter-to, .fade-up-leave-from { opacity: 1; transform: translateY(0); }
.fade-up-enter-active:nth-child(1) { transition-delay: 0.1s; }
.fade-up-enter-active:nth-child(2) { transition-delay: 0.2s; }
.fade-up-enter-active:nth-child(3) { transition-delay: 0.3s; }


.exhibition-viewer-section {
  background-color: #FEFEFE; /* 与第二屏背景色一致或透明，让页面背景透出来 */
  transition: opacity 0.5s ease-in-out, background-color 0.5s ease;
  opacity: 0;
  z-index: 2;
  display: flex; /* 用于居中 entry-card-wrapper */
  align-items: center;
  justify-content: center;
  min-height: 80vh; /* 给它一个最小高度，确保能被 IntersectionObserver 观察到 */
}
.exhibition-viewer-section.visible {
    opacity: 1;
}
.exhibition-viewer-section.activated { /* 展览激活时全屏黑色 */
  background-color: #000;
  color: #fff;
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 1000;
  padding: 0;
  opacity: 1;
}

/* 新增：黑色圆角矩形卡片包裹层 */
.exhibition-entry-card-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%; /* 确保在父容器（section）中可以正确居中 */
}

/* 新增：黑色圆角矩形卡片样式 */
.exhibition-entry-card {
  background-color: #000000; /* 黑色背景 */
  color: #FEFEFE; /* 白色文字 */
  border-radius: 15px; /* 圆角 */
  padding: 20px; /* 内边距 */
  width: 70vw; /* 约占屏幕宽度的70% */
  height: 70vh; /* 约占屏幕高度的70% */
  max-width: 1000px; /* 最大宽度限制 */
  max-height: 700px; /* 最大高度限制 */
  display: flex;
  flex-direction: column;
  justify-content: center; /* 内容垂直居中 */
  align-items: center; /* 内容水平居中 */
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
  position: relative; /* 为了内部绝对定位的 top-bar 和 bottom-bar */
  overflow: hidden; /* 确保内容不出圆角框 */
}

.exhibition-entry-prompt { /* 这个现在是黑色卡片内部的提示内容 */
  text-align: center;
  font-family: 'Georgia', 'Times New Roman', Times, serif;
  width: 100%; /* 占据卡片全部宽度 */
  height: 100%; /* 占据卡片全部高度 */
  display: flex;
  flex-direction: column;
  justify-content: space-between; /* 让主标题和底部按钮上下分布 */
  align-items: center;
  padding: 30px 0; /* 给上下一些空间 */
}
.exhibition-viewer-section.activated .exhibition-entry-card {
    display: none; /* 展览激活时隐藏这个入口卡片 */
}

.exhibition-entry-prompt h2 { /* 主标题 "In the Mood for Love" */
  font-size: clamp(2rem, 6vw, 4.5rem); /* 响应式字体 */
  font-weight: 600;
  color: #FEFEFE; /* 白色 */
  margin: 0; /* 移除默认的 margin */
  flex-grow: 1; /* 让标题占据中间主要空间 */
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 黑色圆角卡片内部的顶部和底部条 */
.entry-card-top-bar, .entry-card-bottom-bar {
    width: calc(100% - 40px); /* 减去卡片内边距 */
    position: absolute;
    left: 20px; /* 与卡片内边距对齐 */
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 0.85rem;
    color: #a0a0a0; /* 灰色文字 */
    font-family: Arial, sans-serif;
}
.entry-card-top-bar {
    top: 20px;
}
.entry-card-bottom-bar {
    bottom: 20px;
    justify-content: flex-end; /* 按钮靠右 */
}
.entry-card-counter { color: #e45a5a; } /* 页码红色 */
.entry-card-title-center { /* 可选，如果顶部中间也有文字 */ }
.entry-card-top-right-text { /* 右上角文字 */ }

/* .enter-button-card {
  background: rgba(50, 50, 50, 0.8);
  border: 1px solid #666;
  color: #e0e0e0;
  padding: 8px 18px;
  font-size: 0.9rem;
  cursor: pointer;
  border-radius: 5px;
  display: flex;
  align-items: center;
  gap: 6px;
  transition: background-color 0.3s, color 0.3s;
} */
.enter-arrow-button .arrow-icon { width: 20px; height: 20px; }
.enter-button-card:hover {
  background-color: rgba(70, 70, 70, 1);
  color: #fff;
}
.enter-button-card svg {
  fill: currentColor;
}


/* 展览激活后的内容容器 (代码不变) */
.exhibition-content-wrapper {
    width: 100%;
    height: 100%;
    position: relative; /* 用于内部 UI 的绝对定位 */
    opacity: 0;
    transition: opacity 0.5s ease-in-out 0.3s; /* 延迟出现，配合背景变黑 */
}
.exhibition-viewer-section.activated .exhibition-content-wrapper {
    opacity: 1;
}


.exhibition-ui-overlay {
  position: absolute; /* 改为 absolute，相对于 .exhibition-content-wrapper */
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  pointer-events: none;
}
.exhibition-ui-overlay > * {
    pointer-events: auto;
}

.top-bar {
  /* position: absolute; */ /* 已被 .exhibition-ui-overlay 的 flex 控制 */
  width: 100%;
  padding: 20px 30px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-sizing: border-box;
  font-family: Arial, sans-serif;
  font-size: 0.9rem;
  color: #ccc;
  background: linear-gradient(to bottom, rgba(0,0,0,0.6), rgba(0,0,0,0));
  z-index: 100;
  flex-shrink: 0; /* 防止被压缩 */
}
.slide-counter {
  color: #e45a5a;
  font-weight: bold;
}
.top-title { /* 保持不变 */ }
.exit-button {
  background: none;
  border: none;
  color: #ccc;
  font-size: 0.9rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 5px;
  padding: 5px 10px;
  border-radius: 4px;
  transition: background-color 0.3s, color 0.3s;
}
.exit-button:hover {
  background-color: rgba(255,255,255,0.1);
  color: #fff;
}
.exit-button svg {
  fill: currentColor;
}


.slides-container {
  flex-grow: 1;
  position: relative;
  width: 100%;
  overflow: hidden;
}

.bottom-bar {
  /* position: absolute; */ /* 已被 .exhibition-ui-overlay 的 flex 控制 */
  width: 100%;
  padding: 20px 30px;
  display: flex;
  justify-content: space-between; /* 调整对齐 */
  align-items: center;
  box-sizing: border-box;
  background: linear-gradient(to top, rgba(0,0,0,0.6), rgba(0,0,0,0));
  z-index: 100;
  flex-shrink: 0;
}
.bottom-extra-text {
    color: #aaa;
    font-size: 0.85rem;
}
.restart-button {
  background: none;
  border: 1px solid #888;
  color: #ccc;
  padding: 8px 15px;
  font-size: 0.8rem;
  cursor: pointer;
  border-radius: 4px;
  display: flex;
  align-items: center;
  gap: 5px;
  transition: color 0.3s, border-color 0.3s;
}
.restart-button:hover {
  color: #fff;
  border-color: #fff;
}
.restart-button svg {
    fill: currentColor;
}


/* 响应式调整 */
@media (max-width: 768px) {
  .screen-section { padding: 30px 15px; }
  .hero-section { min-height: 80vh; }
  .title { font-size: 3rem; }
  .subtitle { font-size: 1.4rem; }
  .details { font-size: 1rem; } /* 调整母亲节页面字体大小 */
  .scroll-prompt { font-size: 0.9rem; } /* 调整母亲节页面字体大小 */


  .creators-section { padding-top: 10px; }

  .exhibition-entry-card {
    width: 85vw;
    height: 75vh;
  }
  .exhibition-entry-prompt h2 { font-size: clamp(1.8rem, 5vw, 3.5rem); }
  .entry-card-top-bar, .entry-card-bottom-bar { font-size: 0.75rem; padding: 0 10px; left:10px; width: calc(100% - 20px); }
  .enter-button-card { font-size: 0.8rem; padding: 7px 15px; }


  .top-bar, .bottom-bar { padding: 15px 20px; font-size: 0.8rem; }
  .slide-counter { margin-right: auto; }
  .top-title { display: none; }
  .exit-button { font-size: 0.8rem; padding: 5px 8px; }
  .bottom-extra-text { font-size: 0.75rem; }
}
@media (max-width: 480px) {
    .title { font-size: 2.5rem; }
    .subtitle { font-size: 1.2rem; }
    .details { font-size: 0.9rem; }
    .scroll-prompt { font-size: 0.8rem; }

    .exhibition-entry-card {
        width: 90vw;
        height: 70vh;
    }
     .entry-card-top-bar, .entry-card-bottom-bar { font-size: 0.7rem; }
}

</style>