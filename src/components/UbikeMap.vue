<template>
  <div class="mt-6">
    <GoogleMap :center="center" :zoom="14" class="w-full h-[500px] rounded-lg shadow-lg">
      <Marker v-for="station in stations" :key="station.id" :position="{ lat: station.lat, lng: station.lng }" />
    </GoogleMap>
  </div>
</template>

<script setup>
import { defineProps, computed } from "vue";
import { GoogleMap, Marker } from "@vue-google-maps/api";

const props = defineProps(["stations"]);

const center = computed(() => {
  return props.stations.length > 0 ? { lat: props.stations[0].lat, lng: props.stations[0].lng } : { lat: 25.0330, lng: 121.5654 };
});
</script>
