<template>
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <div 
      v-for="station in stations" 
      :key="station.id" 
      class="p-4 border rounded shadow-md bg-white"
    >
      <h2 class="text-lg font-bold">{{ station.name }}</h2>
      <p>🚲 可借車輛數: <span class="font-semibold text-blue-600">{{ station.sbi }}</span></p>
      <p>🅿️ 可還車輛數: <span class="font-semibold text-green-600">{{ station.bemp }}</span></p>
      <p>📍 位置: {{ station.area }}</p>
    </div>
  </div>
</template>

<script setup>
defineProps(["stations"]);
</script>
