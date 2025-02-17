<template>
  <div class="flex justify-center mb-4">
    <input
      v-model="search"
      @input="updateSearch"
      placeholder="🔍 搜尋站點名稱或區域"
      class="border p-2 rounded w-2/3"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";

const search = ref("");
const emit = defineEmits(["update-search"]);

const updateSearch = () => {
  emit("update-search", search.value);
};
</script>
