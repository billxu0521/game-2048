
<template>
  <client-only>
    <div class="flex flex-col  justify-between items-center h-screen">
      <div class="flex flex-col items-center">
        header
      </div>
      <div class="flex flex-col items-center">
        Home
      </div>
      <div class="flex py-2 px-4 justify-between">
        <button @click="$router.push('/name')" class="border border-gray-500 rounded-md bg-gray-200 hover:bg-gray-300 mb-10">
        Go to Game
      </button>
      </div>
      
    </div>
  </client-only>
</template>

<script setup lang="ts">

useHead({
  title: "Home",
});

definePageMeta({
  layout: 'front'
})

</script>



    
