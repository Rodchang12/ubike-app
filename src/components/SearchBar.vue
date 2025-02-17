<template>
  <div class="relative flex justify-center mb-4">
    <input
      v-model="search"
      @input="updateSearch"
      @focus="showSuggestions = true"
      placeholder="🔍 搜尋站點名稱或區域"
      class="border p-2 rounded w-2/3"
    />
    
    <!-- 顯示匹配的站點名稱或區域 -->
    <ul v-if="showSuggestions && filteredSuggestions.length > 0" class="absolute w-2/3 bg-white border mt-10 shadow-lg rounded">
      <li 
        v-for="(suggestion, index) in filteredSuggestions" 
        :key="index" 
        @click="selectSuggestion(suggestion)"
        class="p-2 hover:bg-gray-200 cursor-pointer"
      >
        {{ suggestion }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed, defineProps, defineEmits } from "vue";

const search = ref("");
const showSuggestions = ref(false);
const emit = defineEmits(["update-search"]);

const props = defineProps({
  stations: Array, // 從父組件傳入的 Ubike 站點資料
});

//  提取所有站點名稱與區域，去重處理
const uniqueLocations = computed(() => {
  const locations = new Set();
  props.stations.forEach(station => {
    locations.add(station.name);  // 站點名稱
    locations.add(station.area);  // 站點區域
  });
  return Array.from(locations);
});

// 根據使用者輸入篩選建議選項
const filteredSuggestions = computed(() => {
  if (!search.value) return [];
  return uniqueLocations.value.filter(item => item.includes(search.value)).slice(0, 5);
});

// 更新搜尋內容
const updateSearch = () => {
  emit("update-search", search.value);
  showSuggestions.value = search.value.length > 0;
};

// 當使用者點選建議選項時，填入搜尋框並觸發搜尋
const selectSuggestion = (suggestion) => {
  search.value = suggestion;
  showSuggestions.value = false;
  emit("update-search", search.value);
};
</script>
