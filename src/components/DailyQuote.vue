<template>
  <div class="daily-quote">
    <div v-if="loading" class="loading">
      <i class="fas fa-spinner fa-spin"></i>
      加载中...
    </div>
    <div v-else-if="error" class="error">
      <i class="fas fa-exclamation-circle"></i>
      {{ error }}
    </div>
    <div v-else class="quote-content">
      <i class="fas fa-quote-left quote-icon"></i>
      <div class="quote-text">{{ quote.yiyan }}</div>
      <div class="quote-author">—— {{ quote.nick }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const quote = ref({});
const loading = ref(true);
const error = ref('');

const fetchQuote = async () => {
  try {
    const response = await fetch('https://api.nxvav.cn/api/yiyan/?encode=json&charset=utf-8');
    if (!response.ok) {
      throw new Error('API 请求失败');
    }
    quote.value = await response.json();
  } catch (e) {
    error.value = '获取每日一言失败';
    console.error('Quote API Error:', e);
    quote.value = {
      yiyan: "生活明朗，万物可爱",
      nick: "默认"
    };
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  fetchQuote();
});
</script>

<style scoped>
.daily-quote {
  position: fixed;
  bottom: 20px;
  left: calc(50% + 210px); /* 天气卡片宽度的一半 + 间距 */
  transform: translateX(-50%);
  width: 90%;
  max-width: 400px;
  padding: 15px;
  background: linear-gradient(135deg, #FF6B6B, #FF8E8E);
  color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  z-index: 1000;
}

.daily-quote:hover {
  transform: translateX(-50%) translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.loading, .error {
  text-align: center;
  color: white;
}

.error {
  color: #FFE0E0;
}

.quote-content {
  position: relative;
  padding: 10px;
}

.quote-icon {
  position: absolute;
  top: 0;
  left: 0;
  font-size: 20px;
  opacity: 0.5;
}

.quote-text {
  margin: 10px 0;
  font-size: 16px;
  line-height: 1.5;
  text-align: center;
}

.quote-author {
  text-align: right;
  font-style: italic;
  font-size: 14px;
  opacity: 0.8;
  margin-top: 10px;
}

@media (max-width: 900px) {
  .daily-quote {
    bottom: 90px; /* 在小屏幕上显示在天气卡片上方 */
    left: 50%;
  }
}
</style> 