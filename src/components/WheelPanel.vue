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
        prizes.value = newSectors.map((sector, index) => ({
            id: index + 1,
            name: sector,
            value: sector,
            bgColor: `hsl(${(index / newSectors.length) * 360}, 100%, 75%)`,
            color: '#000000',
            probability: 100 / newSectors.length // Equal probability for all sectors
        }))
    }
    
    // 强制重新渲染转盘组件
    wheelVisible.value = false
    setTimeout(() => {
        wheelVisible.value = true
    }, 0)

}, { deep: true, immediate: true })

// Canvas options
const canvasOptions = ref({
    radius: isMobile.value ? 120 : 240,
    textRadius: isMobile.value ? 80 : 180,
    borderColor: '#584b43',
    borderWidth: 6,
    btnText: 'SPIN',
    btnWidth: isMobile.value ? 80 : 120,
    fontSize: isMobile.value ? 14 : 24,
    lineHeight: 24
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
        <FortuneWheel
            v-if="wheelVisible"
            ref="wheelRef"
            :prizes="prizes"
            :canvas="canvasOptions"
            :disabled="isSpinning || !sectors.length"
            :duration="3000"
            style="width: 100%; max-width: 500px;"
            @rotateStart="onRotateStart"
            @rotateEnd="onRotateEnd"
        />
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
}
</style>
