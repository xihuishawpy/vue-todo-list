<template>
  <div class="weather">
    <div v-if="loading" class="loading">
      <i class="fas fa-spinner fa-spin"></i>
      获取天气信息中...
    </div>
    <div v-else-if="error" class="error">
      <i class="fas fa-exclamation-circle"></i>
      {{ error }}
    </div>
    <div v-else class="weather-content">
      <div class="location">
        <i class="fas fa-location-dot location-icon"></i>
        <span class="city">{{ weather.province }} {{ weather.city }}</span>
      </div>
      <div class="weather-info">
        <span class="weather-text">
          <i :class="getWeatherIcon(weather.weather)"></i>
          {{ weather.weather }}
        </span>
        <span class="temperature">
          <i class="fas fa-temperature-half temp-icon"></i>
          {{ weather.temperature }}°C
        </span>
        <span class="wind">
          <i class="fas fa-wind"></i>
          风力{{ weather.windPower }}
        </span>
        <span class="humidity">
          <i class="fas fa-droplet"></i>
          湿度{{ weather.humidity }}%
        </span>
      </div>
      <div class="report-time">
        <i class="far fa-clock"></i>
        更新时间：{{ formatTime(weather.reportTime) }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const formatTime = (timeStr) => {
  if (!timeStr) return '';
  const date = new Date(timeStr);
  return `${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`;
};

const getWeatherIcon = (weather) => {
  const iconMap = {
    '晴': 'fas fa-sun weather-icon sunny',
    '多云': 'fas fa-cloud weather-icon cloudy',
    '阴': 'fas fa-cloud weather-icon overcast',
    '小雨': 'fas fa-cloud-rain weather-icon rainy',
    '中雨': 'fas fa-cloud-showers-heavy weather-icon rainy',
    '大雨': 'fas fa-cloud-showers-heavy weather-icon heavy-rain',
    '雷阵雨': 'fas fa-cloud-bolt weather-icon thunder',
    '雾': 'fas fa-smog weather-icon foggy',
    '雪': 'fas fa-snowflake weather-icon snowy'
  };
  return iconMap[weather] || 'fas fa-cloud';
};

const weather = ref({});
const loading = ref(true);
const error = ref('');

const getWeather = async () => {
  try {
    // 获取天气信息
    const weatherUrl = `https://restapi.amap.com/v3/weather/weatherInfo?city=440306&key=911b0dbbdef55165f2ebe0f00c9a9f13`;
    const weatherResponse = await fetch(weatherUrl);
    const weatherData = await weatherResponse.json();

    if (weatherData.status !== '1' || !weatherData.lives?.[0]) {
      throw new Error('获取天气信息失败');
    }

    const live = weatherData.lives[0];
    weather.value = {
      province: live.province,
      city: live.city,
      weather: live.weather,
      temperature: live.temperature,
      windPower: live.windpower,
      humidity: live.humidity,
      reportTime: live.reporttime
    };
  } catch (e) {
    error.value = '获取天气信息失败，请稍后再试';
    console.error('Weather API Error:', e);
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  getWeather();
});
</script>

<style scoped>
.weather {
  position: fixed;
  bottom: 20px;
  left: calc(50% - 310px);
  width: 90%;
  max-width: 400px;
  padding: 15px;
  background: linear-gradient(135deg, #6dd5ed, #2193b0);
  color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  z-index: 1000;
}

.weather:hover {
  transform: translate(-50%, -2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.loading, .error {
  text-align: center;
  color: white;
}

.error {
  color: #ff6b6b;
}

.weather-content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.location {
  margin-bottom: 10px;
}

.location-icon {
  margin-right: 8px;
  color: #ffd700;
}

.city {
  font-size: 18px;
  font-weight: bold;
  color: white;
}

.weather-info {
  display: flex;
  gap: 20px;
  align-items: center;
  margin-bottom: 10px;
  flex-wrap: wrap;
  justify-content: center;
}

.temperature {
  font-size: 24px;
  font-weight: bold;
  color: white;
}

.temp-icon {
  color: #ff9f43;
  margin-right: 5px;
}

.weather-text, .wind, .humidity {
  color: white;
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: 5px;
}

.weather-icon {
  font-size: 24px;
  margin-right: 8px;
}

.sunny { color: #ffd700; }
.cloudy { color: #f1f2f6; }
.rainy { color: #a5b1c2; }
.thunder { color: #ffd700; }
.foggy { color: #dfe4ea; }
.snowy { color: white; }

.report-time {
  color: rgba(255, 255, 255, 0.8);
  font-size: 12px;
  margin-top: 5px;
  display: flex;
  align-items: center;
  gap: 5px;
}

@keyframes float {
  0% { transform: translateY(0px); }
  50% { transform: translateY(-5px); }
  100% { transform: translateY(0px); }
}

.weather-icon {
  animation: float 3s ease-in-out infinite;
}

@media (max-width: 900px) {
  .weather {
    left: 50%;
    transform: translateX(-50%);
    bottom: 90px;
  }
}
</style> 