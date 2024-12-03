<template>
    <div class="converter">
      <div class="input-group">
        <label>
          Convert from:
          <select v-model="convertFrom">
            <option value="px">Pixels (px)</option>
            <option value="vw">Viewport Width (vw)</option>
            <option value="vh">Viewport Height (vh)</option>
          </select>
        </label>
      </div>
  
      <div class="input-group">
        <input 
          type="number" 
          :value="inputValue"
          @input="handleInput"
          placeholder="Enter value"
        />
      </div>
  
      <div class="result-group">
        <div class="result-item">
          <strong>Pixels (px):</strong> 
          {{ results.px.toFixed(2) }}px
        </div>
        <div class="result-item">
          <strong>Viewport Width (vw):</strong> 
          {{ results.vw.toFixed(2) }}vw
        </div>
        <div class="result-item">
          <strong>Viewport Height (vh):</strong> 
          {{ results.vh.toFixed(2) }}vh
        </div>
      </div>
  
      <div class="viewport-info">
        <p>Current Viewport: 
          {{ viewportWidth }}px wide, 
          {{ viewportHeight }}px high
        </p>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, onMounted, onUnmounted } from 'vue'
  import type { ConversionResults, ConversionType } from '@/types/converter'
  
  // Reactive state
  const inputValue = ref<number | null>(null)
  const convertFrom = ref<ConversionType>('px')
  const viewportWidth = ref<number>(window.innerWidth)
  const viewportHeight = ref<number>(window.innerHeight)
  const results = ref<ConversionResults>({
    px: 0,
    vw: 0,
    vh: 0
  })
  
  // Conversion methods
  const pxToVw = (px: number): number => 
    (px / viewportWidth.value) * 100
  
  const pxToVh = (px: number): number => 
    (px / viewportHeight.value) * 100
  
  const vwToPx = (vw: number): number => 
    (vw / 100) * viewportWidth.value
  
  const vhToPx = (vh: number): number => 
    (vh / 100) * viewportHeight.value
  
  // Conversion logic
  const calculateConversion = () => {
    if (!inputValue.value) {
      results.value = { px: 0, vw: 0, vh: 0 }
      return
    }
  
    const value = inputValue.value
  
    switch(convertFrom.value) {
      case 'px':
        results.value = {
          px: value,
          vw: pxToVw(value),
          vh: pxToVh(value)
        }
        break
      case 'vw':
        results.value = {
          px: vwToPx(value),
          vw: value,
          vh: vwToVh(value)
        }
        break
      case 'vh':
        results.value = {
          px: vhToPx(value),
          vw: vhToVw(value),
          vh: value
        }
        break
    }
  }
  
  // Event handlers
  const handleInput = (event: Event) => {
    const target = event.target as HTMLInputElement
    inputValue.value = parseFloat(target.value)
    calculateConversion()
  }
  
  // Update viewport size on resize
  const updateViewportSize = () => {
    viewportWidth.value = window.innerWidth
    viewportHeight.value = window.innerHeight
    calculateConversion()
  }
  
  // Lifecycle hooks
  onMounted(() => {
    window.addEventListener('resize', updateViewportSize)
  })
  
  onUnmounted(() => {
    window.removeEventListener('resize', updateViewportSize)
  })
  
function vwToVh(_value: number): number {
    throw new Error('Function not implemented.');
}

function vhToVw(_value: number): number {
    throw new Error('Function not implemented.');
}
</script>
  
  <style scoped>
  .converter {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }
  
  .input-group {
    display: flex;
    flex-direction: column;
  }
  
  input, select {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
  
  .result-group {
    background-color: #f9f9f9;
    padding: 15px;
    border-radius: 5px;
  }
  
  .result-item {
    margin-bottom: 10px;
  }
  
  .viewport-info {
    text-align: center;
    color: #666;
    font-size: 0.9em;
  }
  </style>