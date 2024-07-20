<script setup lang="ts">

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full object-cover bg-black ">
    <div class="container-mobile relative z-1 flex min-h-screen flex-col justify-between px-5 pt-7 pb-14 bg-white ">
      <div class="container-mobile absolute inset-0 z-0 overflow-hidden">
        <slot />
      </div>
     
    </div>
    </div>
</template>

<style scoped>

.container-mobile{
  margin-left: auto;
  margin-right: auto;
  max-width: 480px;
  width: 100%;
}
.pointer-events-none{
  pointer-events: none;
}
</style>
