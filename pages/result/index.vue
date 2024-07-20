
<template>
    <client-only>
      <div class="flex flex-col  justify-between items-center h-screen">
        <div class="flex flex-col items-center">
          Result header
        </div>
        <div class="flex flex-col items-center w-full">
            Finished
            
            
            <div class="grid grid-cols-2 gap-4 w-full">
              <div><SingleRating/></div>
              <div><SingleRating/></div>
              <div><SingleRating/></div>
              <div><SingleRating/></div>
            </div>
        </div>
        <div class="flex py-2 px-4 justify-between mb-10">
          
        </div>
        
      </div>
    </client-only>
  </template>

<script setup lang="ts">
useHead({
    title: `Result`,
});

definePageMeta({
  layout: 'front'
})

</script>