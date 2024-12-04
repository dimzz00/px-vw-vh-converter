<template>
    <div class="converter">
        <div class="input-group mt-5">
            <label class="text-left">
                <span class="text-black"> Convert from: </span>
                <select
                    id="select-convert"
                    v-model="convertFrom"
                    @change="convertFormSelect"
                >
                    <option value="px">Pixels (px)</option>
                    <option value="vw">Viewport Width (vw)</option>
                    <option value="vh">Viewport Height (vh)</option>
                </select>
            </label>
        </div>

        <div class="input-group">
            <span class="text-left text-black">{{ convertSelect }}</span>
            <input
                id="input"
                type="number"
                :value="inputValue"
                @input="handleInput"
                placeholder="Enter value"
            />
            <span class="text-left text-black text-xl mt-5 mb-2 font-bold">
                Viewport Setting
            </span>
            <div>
                <label class="width mr-5">
                    <span class="text-black">Width : </span>
                    <input
                        id="view-wide"
                        type="number"
                        :value="viewWideValue"
                        @input="handleInput"
                        placeholder="Enter value"
                    />
                </label>
                <label class="height">
                    <span class="text-black">Height : </span>
                    <input
                        id="view-height"
                        type="number"
                        :value="viewHeightValue"
                        @input="handleInput"
                        placeholder="Enter value"
                    />
                </label>
            </div>
        </div>

        <div class="result-group">
            <div class="container-result w-fit text-left">
                <div class="result-item">
                    <strong>Pixels (px)</strong>
                    <span> : {{ results.px.toFixed(2) || 0 }}px </span>
                </div>
                <div class="result-item">
                    <strong>Viewport Width (vw)</strong>
                    <span> : {{ results.vw.toFixed(2) || 0 }}vw </span>
                </div>
                <div class="result-item">
                    <strong>Viewport Height (vh)</strong>
                    <span> : {{ results.vh.toFixed(2) || 0 }}vh </span>
                </div>
            </div>
        </div>

        <div class="viewport-info">
            <p>
                Current Viewport: {{ viewportWidth }}px wide,
                {{ viewportHeight }}px high (Scale screen resolution now)
            </p>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import type { ConversionResults, ConversionType } from "@/types/converter";

// Reactive state
const inputValue = ref<number | 0>(0);
const viewWideValue = ref<number | 0>(0);
const viewHeightValue = ref<number | 0>(0);
const convertFrom = ref<ConversionType>("px");
const convertSelect = ref<String>("Pixel (px)");
const viewportWidth = ref<number>(window.innerWidth);
const viewportHeight = ref<number>(window.innerHeight);
const results = ref<ConversionResults>({
    px: 0,
    vw: 0,
    vh: 0,
});

// Conversion methods
const pxToVw = (px: number): number => (px / viewWideValue.value) * 100;

const pxToVh = (px: number): number => (px / viewHeightValue.value) * 100;

const vwToPx = (vw: number): number => (vw / 100) * viewWideValue.value;

const vhToPx = (vh: number): number => (vh / 100) * viewHeightValue.value;

const vwToVh = (vw: number): number => pxToVh(vwToPx(vw));

const vhToVw = (vh: number): number => pxToVw(vhToPx(vh));

// confertFormSelect const
const convertFormSelect = () => {
    const tempTarget = document.getElementById(
        "select-convert",
    ) as HTMLInputElement;
    const targetValue = tempTarget.value;
    let dataReturn = ref<string>("");
    switch (targetValue) {
        case "px":
            dataReturn.value = "Pixel (px)";
            break;
        case "vw":
            dataReturn.value = "Viewport Width (vw)";
            break;
        case "vh":
            dataReturn.value = "Viewport Height (vh)";
            break;
    }
    convertSelect.value = dataReturn.value;
};

// Conversion logic
const calculateConversion = () => {
    if (!inputValue.value) {
        results.value = { px: 0, vw: 0, vh: 0 };
        return;
    }

    const value = inputValue.value;

    switch (convertFrom.value) {
        case "px":
            results.value = {
                px: value,
                vw: pxToVw(value),
                vh: pxToVh(value),
            };
            break;
        case "vw":
            results.value = {
                px: vwToPx(value),
                vw: value,
                vh: vwToVh(value),
            };
            break;
        case "vh":
            results.value = {
                px: vhToPx(value),
                vw: vhToVw(value),
                vh: value,
            };
            break;
    }
};

// Event handlers
const handleInput = (event: Event) => {
    const target = event.target as HTMLInputElement;
    const inputType = target.id;
    switch (inputType) {
        case "input":
            inputValue.value = parseFloat(target.value);
            break;
        case "view-wide":
            viewWideValue.value = parseFloat(target.value);
            break;
        case "view-height":
            viewHeightValue.value = parseFloat(target.value);
            break;
    }
    calculateConversion();
};

// Update viewport size on resize
const updateViewportSize = () => {
    viewportWidth.value = window.innerWidth;
    viewportHeight.value = window.innerHeight;
    viewWideValue.value = viewportWidth.value;
    viewHeightValue.value = viewportHeight.value;
};

// Lifecycle hooks
onMounted(() => {
    viewWideValue.value = viewportWidth.value;
    viewHeightValue.value = viewportHeight.value;
    window.addEventListener("resize", updateViewportSize);
});

onUnmounted(() => {
    window.removeEventListener("resize", updateViewportSize);
});
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

input,
select {
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
    display: grid;
    grid-template-columns: 200px auto;
    margin-bottom: 10px;
    color: black;
}

.viewport-info {
    text-align: center;
    color: #666;
    font-size: 0.9em;
}
</style>
