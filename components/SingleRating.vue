<template>
    <div class="flex items-center w-full">
      <span class="mr-2"></span>
      <div class="relative w-full max-w-sm h-10 bg-gray-800 rounded-full overflow-hidden">
        <div class="absolute inset-0 flex space-x-1 p-1">
          <div
            v-for="(box,index) in boxes"
            :key="index"
            class="w-full h-full bg-green-500"
          ></div>
        </div>
        <div class="absolute inset-0 w-full h-full border-4 border-pink-500 rounded-full"></div>
      </div>
    </div>
  </template>

<script setup lang="ts">
const boxes = 10;
</script>

<style scoped>
.rating-bar {
    background-color: blue; /* Replace 'blue' with your desired color */
    height: 10px; /* Adjust the height as needed */
}

.rating-bar-fill {
    background-color: yellow; /* Replace 'yellow' with your desired color */
    height: 100%;
}
</style>