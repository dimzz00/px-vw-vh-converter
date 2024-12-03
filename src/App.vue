<template>
  <div class="container">
    <h1>Pixel / Viewport Converter</h1>
    <ConverterComponent />
  </div>
</template>

<script setup lang="ts">
import ConverterComponent from '@/components/ConverterComponent.vue'
</script>

<style>
body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f4f4;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  margin: 0;
}

.container {
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 30px;
  width: 100%;
  max-width: 500px;
}

h1 {
  text-align: center;
  color: #333;
}
</style>