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
      <div class="quote-text">
        <div class="en">{{ quote.content }}</div>
        <div class="zh">{{ quote.note }}</div>
      </div>
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
    const response = await fetch('http://open.iciba.com/dsapi/');
    
    if (!response.ok) {
      throw new Error('API 请求失败');
    }
    const data = await response.json();
    quote.value = data;
  } catch (e) {
    error.value = '获取每日一言失败';
    console.error('Quote API Error:', e);
    quote.value = {
      content: "The only thing constant in life is change.",
      note: "生活中唯一不变的就是变化。"
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
  right: 20px;
  width: 300px;
  padding: 15px;
  background: linear-gradient(135deg, #4B79A1, #283E51);
  color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  z-index: 1000;
}

.daily-quote:hover {
  transform: translateY(-2px);
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
  line-height: 1.5;
  text-align: center;
}

.en {
  font-size: 16px;
  margin-bottom: 8px;
}

.zh {
  font-size: 14px;
  opacity: 0.9;
  color: #e8e8e8;
}

@media (max-width: 900px) {
  .daily-quote {
    position: fixed;
    bottom: 90px;
    right: 50%;
    transform: translateX(50%);
    width: 90%;
    max-width: 400px;
  }

  .daily-quote:hover {
    transform: translate(50%, -2px);
  }
}
</style> 