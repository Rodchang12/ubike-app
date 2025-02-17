<template>
  <div class="container mx-auto p-6">
    <h1 class="text-2xl font-bold text-center mb-4">🚲 Ubike 站點資訊</h1>

    <div v-if="loading" class="text-center">載入中...</div>
    
    <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      <div 
        v-for="station in stations" 
        :key="station.id" 
        class="p-4 border rounded shadow-md bg-white"
      >
        <h2 class="text-lg font-bold">{{ station.sna }}</h2>
        <p>🚲 可借車輛數: <span class="font-semibold text-blue-600">{{ station.sbi }}</span></p>
        <p>🅿️ 可還車輛數: <span class="font-semibold text-green-600">{{ station.bemp }}</span></p>
        <p>📍 位置: {{ station.sarea }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const stations = ref([]);
const loading = ref(true);

const fetchUbikeData = async () => {
  try {
    const response = await fetch(
      "https://tcgbusfs.blob.core.windows.net/blobyoubike/YouBikeTP.json"
    );
    const data = await response.json();

    // API 回傳的資料格式需轉換
    stations.value = Object.values(data.retVal).map(station => ({
      id: station.sno,
      sna: station.sna, // 站點名稱
      sbi: station.sbi, // 可借車輛數
      bemp: station.bemp, // 可還車輛數
      sarea: station.sarea // 所在區域
    }));
  } catch (error) {
    console.error("Error fetching Ubike data:", error);
  } finally {
    loading.value = false;
  }
};

onMounted(fetchUbikeData);
</script>

<style>
body {
  @apply bg-gray-100;
}
</style>
