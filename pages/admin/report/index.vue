
<template>
  <div v-for="(data, index) in result" key="index">
    {{ data.content }}
  </div>
  
</template>

<script setup lang="ts">
import axios from 'axios';

useHead({
    title: `Report`,
});

let result = ref(null);
onMounted(async () => {
  try {
    const response = await axios.get('/api/selected-answer', {
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer your_token'
      }
    });
    result.value = response.data.answerData;
    console.log(response.data);
  } catch (error) {
    console.error('Error:', error);
  }
  })


</script>