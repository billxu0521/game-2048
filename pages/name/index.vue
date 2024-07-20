
<template>
  <client-only>
    <div class="flex flex-col  justify-between items-center h-screen">
      <div class="flex flex-col items-center">
        Name header
      </div>
      <div class="flex flex-col items-center ">
        <input class="border border-gray-500 rounded-md p-4" type="text" v-model="playerName" placeholder="Enter text" required>
        <select class="border border-gray-500 rounded-md p-4" v-model="selectedRegion" placeholder="Enter Region">
          <option disabled value="">選擇縣市</option>
          <option v-for="region in regions" :key="region">{{ region }}</option>
        </select>
      </div>
      <div class="flex py-2 px-4 justify-between mb-10">
        <button @click="$router.push('/')" class="border border-gray-500 rounded-md bg-gray-200 hover:bg-gray-300 ">
        Back
      </button>
      <button @click="$router.push('/game')" class="border border-gray-500 rounded-md bg-gray-200 hover:bg-gray-300 ">
       Next
      </button>
      </div>
      
    </div>
  </client-only>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router';
const router = useRouter();

useHead({
  title: `Name Index`,
});
definePageMeta({
  layout: 'front'
})

const playerName = ref('');
const selectedRegion = ref('');
const regions = [
  '基隆市',
  '台北市',
  '新北市',
  '桃園市',
  '新竹市',
  '新竹縣',
  '苗栗縣',
  '台中市',
  '彰化縣',
  '南投縣',
  '雲林縣',
  '嘉義市',
  '嘉義縣',
  '台南市',
  '高雄市',
  '屏東縣',
];
</script>