<template>
  <div class="time-card">
    <div class="date-info">
      <div class="current-time">{{ currentTime }}</div>
      <div class="date-details">
        {{ currentDate }} {{ weekDay }}
      </div>
    </div>
    <div class="extra-info">
      <div class="year-day">
        今年第 {{ dayOfYear }} 天
      </div>
      <div class="work-time" :class="{ 'off-work': isOffWork }">
        {{ workTimeMessage }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const currentTime = ref('');
const currentDate = ref('');
const weekDay = ref('');
const dayOfYear = ref(0);
const workTimeMessage = ref('');
const isOffWork = ref(false);

const weekDays = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];

const updateTime = () => {
  const now = new Date();
  
  // 更新时间
  currentTime.value = now.toLocaleTimeString('zh-CN', { 
    hour: '2-digit', 
    minute: '2-digit', 
    second: '2-digit' 
  });
  
  // 更新日期
  currentDate.value = now.toLocaleDateString('zh-CN', {
    month: 'long',
    day: 'numeric'
  });
  
  // 更新星期
  weekDay.value = weekDays[now.getDay()];
  
  // 计算今年的第几天
  const start = new Date(now.getFullYear(), 0, 0);
  const diff = now - start;
  const oneDay = 1000 * 60 * 60 * 24;
  dayOfYear.value = Math.floor(diff / oneDay);
  
  // 计算距离下班时间
  const offWork = new Date(now.getFullYear(), now.getMonth(), now.getDate(), 18, 0, 0);
  const timeUntilOffWork = offWork - now;
  
  if (timeUntilOffWork <= 0) {
    workTimeMessage.value = '已经下班啦！';
    isOffWork.value = true;
  } else {
    const hours = Math.floor(timeUntilOffWork / (1000 * 60 * 60));
    const minutes = Math.floor((timeUntilOffWork % (1000 * 60 * 60)) / (1000 * 60));
    workTimeMessage.value = `距离下班还有 ${hours}小时${minutes}分钟`;
    isOffWork.value = false;
  }
};

let timer;

onMounted(() => {
  updateTime();
  timer = setInterval(updateTime, 1000);
});

onUnmounted(() => {
  clearInterval(timer);
});
</script>

<style scoped>
.time-card {
  position: fixed;
  bottom: 20px;
  left: calc(50% + 320px);
  width: 300px;
  padding: 15px;
  background: linear-gradient(135deg, #20BF55, #01BAEF);
  color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: box-shadow 0.3s ease;
  z-index: 1000;
}

.time-card:hover {
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.current-time {
  font-size: 32px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 5px;
}

.date-details {
  font-size: 16px;
  text-align: center;
  margin-bottom: 15px;
}

.extra-info {
  font-size: 14px;
  opacity: 0.9;
  text-align: center;
}

.year-day {
  margin-bottom: 5px;
}

.work-time {
  color: #FFE66D;
}

.off-work {
  color: #98FF98;
}

@media (max-width: 900px) {
  .time-card {
    position: fixed;
    bottom: 180px;
    left: 50%;
    transform: translateX(-50%);
    width: 90%;
    max-width: 400px;
  }

  .time-card:hover {
    transform: translateX(-50%);
  }
}
</style> 