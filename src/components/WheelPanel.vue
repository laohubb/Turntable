<script setup>
import { ref, watch, onMounted,defineEmits } from 'vue'


const emit = defineEmits(['spin'])

const props = defineProps({
    sectors: {
        type: Array,
        required: true,
    },
})

const wheelCanvas = ref(null)
const isSpinning = ref(false)

const drawWheel = () => {
    const canvas = wheelCanvas.value;
    const ctx = canvas.getContext('2d');
    const centerX = canvas.width / 2;
    const centerY = canvas.height / 2;
    const radius = 240;

    const numOptions = props.sectors.length;
    const angleStep = (2 * Math.PI) / numOptions;

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    props.sectors.forEach((option, index) => {
        const startAngle = index * angleStep;
        const endAngle = startAngle + angleStep;

        // 绘制扇区
        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.arc(centerX, centerY, radius, startAngle, endAngle);
        ctx.closePath();

        // 设置填充颜色和边框
        ctx.fillStyle = `hsl(${(index / numOptions) * 360}, 100%, 75%)`;
        ctx.fill();
        ctx.stroke();

        // 计算文本位置
        const textAngle = startAngle + angleStep / 2;
        const textX = centerX + (radius / 1.5) * Math.cos(textAngle);
        const textY = centerY + (radius / 1.5) * Math.sin(textAngle);

        // 绘制文本
        ctx.fillStyle = '#000';
        ctx.font = '16px Arial';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText(option, textX, textY);
    });
};

const spinWheel = () => {
    emit('spin')
    isSpinning.value = true
    const canvas = wheelCanvas.value
    const ctx = canvas.getContext('2d')
    let startAngle = Math.random() * 2 * Math.PI // 初始随机角度
    const spinDuration = 3000
    const spinEndTime = Date.now() + spinDuration

    const animateSpin = () => {
        const currentTime = Date.now()
        if (currentTime < spinEndTime) {
            const remainingTime = spinEndTime - currentTime
            const easeIn = t => Math.pow(t, 3) // 加速效果
            const spinSpeed = easeIn((spinDuration - remainingTime) / spinDuration) * 30 // 控制旋转速度

            startAngle += (Math.PI / spinSpeed)
            ctx.clearRect(0, 0, canvas.width, canvas.height)
            ctx.save()
            ctx.translate(canvas.width / 2, canvas.height / 2)
            ctx.rotate(startAngle)
            ctx.translate(-canvas.width / 2, -canvas.height / 2)
            drawWheel()
            ctx.restore()
            requestAnimationFrame(animateSpin)
        } else {
            isSpinning.value = false
        }
    }

    requestAnimationFrame(animateSpin)
}



onMounted(() => {
    if (props.sectors.length > 0) {
        drawWheel()
    }
})

watch(() => props.sectors, (newSectors) => {
    if (newSectors.length > 0) {
        drawWheel()
    }
}, {
    deep: true
})
</script>

<template>
    <div class="wheel-container">
        <canvas ref="wheelCanvas" width="500" height="500" class=""></canvas>
        <v-btn icon="mdi-triangle-outline" @click="spinWheel" :disabled="isSpinning || !sectors.length"></v-btn>
    </div>
</template>

<style scoped>
.wheel-container {
    display: flex;
    flex-direction: column;
    align-items: center;
}
</style>
