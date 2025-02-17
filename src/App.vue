<template>
  <div class="container mx-auto p-6">
    <h1 class="text-2xl font-bold text-center text-blue-600 mb-4">🚲 Ubike 即時查詢系統</h1>
    
    <div v-if="userLocation" class="text-center text-gray-500 mb-4">
      📍 你的當前位置：<span class="font-semibold text-blue-500">{{ userLocation }}</span>
    </div>

    <SearchBar @update-search="searchTerm = $event" :stations="stations" />
    <UbikeList :stations="filteredStations" :lastUpdated="lastUpdated" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import SearchBar from "./components/SearchBar.vue";
import UbikeList from "./components/UbikeList.vue";

const stations = ref([]);
const searchTerm = ref("");

const lastUpdated = ref("");
const userLocation = ref(""); 


// 取得 Ubike API 數據
const fetchUbikeData = async () => {
  try {
    const response = await fetch(
      "https://tcgbusfs.blob.core.windows.net/blobyoubike/YouBikeTP.json"
    );
    const data = await response.json();

    stations.value = Object.values(data.retVal).map(station => ({
      id: station.sno,
      name: station.sna, // 站點名稱
      sbi: station.sbi, // 可借數量
      bemp: station.bemp, // 可還數量
      area: station.sarea, // 站點區域
      detail: station.ar, // 站點地址
      lat: parseFloat(station.lat), // 緯度
      lng: parseFloat(station.lng) // 經度
    }));

    lastUpdated.value = new Date().toLocaleTimeString(); // 更新最後查詢時間
  } catch (error) {
    console.error("Error fetching Ubike data:", error);
  }
};

// 取得使用者地理位置
const getUserLocation = () => {
  if ("geolocation" in navigator) {
    navigator.geolocation.getCurrentPosition(
      async (position) => {
        const { latitude, longitude } = position.coords;

        try {
          // 呼叫 OpenStreetMap Nominatim API
          const response = await fetch(
            `https://nominatim.openstreetmap.org/reverse?format=json&lat=${latitude}&lon=${longitude}`
          );
          const data = await response.json();
          console.log("取得的地理資訊:", data);

          // 解析縣市+區域資訊
          const city = data.address.city || data.address.county || "未知城市";
          const district = data.address.district || data.address.suburb || data.address.village || "未知區域";
          
          // 組合縣市+區域
          userLocation.value = `${city} ${district}`;
        } catch (error) {
          console.error("Error fetching location data:", error);
          userLocation.value = "無法取得位置";
        }
      },
      () => {
        userLocation.value = "無法存取位置資訊";
      }
    );
  } else {
    userLocation.value = "瀏覽器不支援地理定位";
  }
};


// 每 60 秒更新一次數據
onMounted(() => {
  fetchUbikeData();
  getUserLocation();
  setInterval(fetchUbikeData, 60000); // 60 秒更新一次
});

// 根據搜尋篩選站點
const filteredStations = computed(() => {
  return stations.value.filter(station =>
    station.name.includes(searchTerm.value) || station.area.includes(searchTerm.value)
  );
});
</script>

<style>
body {
  @apply bg-blue-50;
}
</style>
