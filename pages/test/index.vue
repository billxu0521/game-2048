
<template>
  Test

</template>

<script setup lang="ts">
useHead({
  title: `Test`,
});

definePageMeta({
layout: 'front'
})

onMounted(async () => {
  try {
    // Send a POST request to the API
    const postData = {
      answer: 'test_' + new Date().toLocaleString()
    };

    const response = await axios.post('/api/selected-answer', postData, {
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer your_token'
      }
    });

    console.log(response.data);
  } catch (error) {
    console.error('Error:', error);
  }
  })
</script>