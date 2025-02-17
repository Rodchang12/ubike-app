<template>
  <div class="container mx-auto p-6">
    <h1 class="text-2xl font-bold text-center mb-4">🚲 Ubike 站點資訊</h1>

    <SearchBar @update-search="searchTerm = $event" />
    <UbikeList :stations="filteredStations" />
    <UbikeMap v-if="isLoaded" :stations="filteredStations" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useLoadScript } from "@vue-google-maps/api";
import SearchBar from "./components/SearchBar.vue";
import UbikeList from "./components/UbikeList.vue";
import UbikeMap from "./components/UbikeMap.vue";

const stations = ref([]);
const searchTerm = ref("");

// ✅ 定期更新 Ubike 資料
const fetchUbikeData = async () => {
  try {
    const response = await fetch(
      "https://tcgbusfs.blob.core.windows.net/blobyoubike/YouBikeTP.json"
    );
    const data = await response.json();
    stations.value = Object.values(data.retVal).map(station => ({
      id: station.sno,
      name: station.sna,
      sbi: station.sbi, // 可借數量
      bemp: station.bemp, // 可還數量
      lat: parseFloat(station.lat),
      lng: parseFloat(station.lng),
      area: station.sarea,
    }));
  } catch (error) {
    console.error("Error fetching Ubike data:", error);
  }
};

onMounted(() => {
  fetchUbikeData();
  setInterval(fetchUbikeData, 60000); // 每 60 秒更新一次數據
});

// ✅ 根據搜尋篩選站點
const filteredStations = computed(() => {
  return stations.value.filter(station =>
    station.name.includes(searchTerm.value) || station.area.includes(searchTerm.value)
  );
});

// ✅ 載入 Google Maps API
const { isLoaded } = useLoadScript({
  googleMapsApiKey: "你的_API_KEY",
});
</script>

<style>
body {
  @apply bg-gray-100;
}
</style>
