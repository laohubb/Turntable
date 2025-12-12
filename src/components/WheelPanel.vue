<script setup>
import { ref, watch, onMounted, defineEmits } from 'vue'
import FortuneWheel from 'vue-fortune-wheel'
import 'vue-fortune-wheel/style.css'

const emit = defineEmits(['spin'])

const props = defineProps({
    sectors: {
        type: Array,
        required: true,
    },
})

const wheelRef = ref(null)
const isSpinning = ref(false)
const isMobile = ref(false)

onMounted(() => {
    const userAgent = navigator.userAgent || navigator.vendor || window.opera
    isMobile.value = /android|webos|iphone|ipad|ipod|blackberry|iemobile|opera mini/i.test(userAgent.toLowerCase())
})

// Transform sectors into the format required by vue-fortune-wheel
const prizes = ref([])
const wheelVisible = ref(true)

watch(() => props.sectors, (newSectors) => {
    console.log('sectors changed:', newSectors);

    if (newSectors && newSectors.length > 0) {
        const colorPalette = [
            '#FFB3BA', // Cherry
            '#FFDFBA', // Peach
            '#FFFFBA', // Lemon
            '#BAFFC9', // Mint
            '#BAE1FF', // Sky
            '#E6B3FF', // Lavender
            '#D4A5A5', // Dusty Rose
            '#A8E6CF', // Light Green
        ];

        prizes.value = newSectors.map((sector, index) => ({
            id: index + 1,
            name: sector,
            value: sector,
            bgColor: colorPalette[index % colorPalette.length],
            color: '#584b43', // Soft brown text for better contrast with pastels
            probability: Math.floor(100 / newSectors.length + (index < 100 % newSectors.length ? 1 : 0)) // 确保是整数且总和为100
        }))
    }

    // 强制重新渲染转盘组件
    wheelVisible.value = false
    setTimeout(() => {
        wheelVisible.value = true
    }, 0)
    isSpinning.value = false
}, { deep: true, immediate: true })

// Canvas options
const dpr = 2 // High resolution multiplier
const canvasOptions = ref({
    radius: (isMobile.value ? 120 : 240) * dpr,
    textRadius: (isMobile.value ? 80 : 180) * dpr,
    borderColor: '#FFFFFF', // Clean white border
    borderWidth: 6 * dpr,
    btnText: 'GO', // Simpler text
    btnBgColor: '#000000', // We can try to customizing this if the lib supports it, otherwise default styling might persist. 
    // Note: vue-fortune-wheel 1.x might not support dynamic btnBgColor easily via this prop, 
    // but let's try standard naming conventions or rely on CSS overrides if needed.
    // Actually the library usually takes `btnBorderColor` or similar if documented.
    // Given I can't check docs, I'll try to set specific properties that are common.
    btnWidth: (isMobile.value ? 50 : 80) * dpr, // Slightly smaller button
    fontSize: (isMobile.value ? 14 : 20) * dpr,
    lineHeight: 24 * dpr
})

// When the wheel finishes spinning
const onRotateEnd = (prize) => {
    isSpinning.value = false
    emit('spin', prize.value)
}

// When the wheel starts spinning
const onRotateStart = () => {
    isSpinning.value = true
    emit('spin')
}
</script>

<template>
    <div class="wheel-container">
        <FortuneWheel v-if="wheelVisible" ref="wheelRef" :prizes="prizes" :canvas="canvasOptions"
            :disabled="isSpinning || !sectors.length" :duration="3000" style="width: 100%; max-width: 500px;"
            @rotateStart="onRotateStart" @rotateEnd="onRotateEnd" />
    </div>
</template>

<style scoped>
.wheel-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    padding: 1rem;
    filter: drop-shadow(0 10px 15px rgba(0, 0, 0, 0.1));
    /* Soft shadow for depth */
}
</style>
