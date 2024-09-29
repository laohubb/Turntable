<template>

  <v-dialog v-model="dialogVisible" max-width="400">
   
    <v-card>
      <v-card-title>
        <span class="headline">编辑选项</span>
      </v-card-title>
      <v-card-text>
        <v-text-field label="分类" v-model="name"></v-text-field>
        <v-textarea v-model="multilineText" @change="print" label="选项" rows="5" outlined></v-textarea>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn @click="deleteItem">删除</v-btn>
        <v-btn @click="save">保存</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <WheelPanel class="mt-10" :sectors="textArray" @spin="spin" />
  <v-sheet class="px-2 w-full mt-2" :class="{ 'justify-center': !isMobile }">
    <v-chip v-for="item in optionsBtn" :key="item.name" @click="changeArray(item)">{{ item.name }}</v-chip>
    <v-btn variant="text"  size="x-small"  icon="mdi-pencil-outline" @click="dialogVisible = true"></v-btn>

  </v-sheet>
  <ConfettiDemo v-if="confettiVisible" class="confetti"></ConfettiDemo>
</template>

<script setup>
import { ref ,onMounted} from 'vue'
import { useStorage } from '@vueuse/core'
const isMobile = ref(false)
onMounted(() => {
    const userAgent = navigator.userAgent || navigator.vendor || window.opera
    isMobile.value = /android|webos|iphone|ipad|ipod|blackberry|iemobile|opera mini/i.test(userAgent.toLowerCase())
   
})
const optionsBtn = useStorage('options', [
  {
    name: '是/否',
    options: ['是', '否', '是', '否']
  }
])

const name = ref('')
const multilineText = ref('')
const dialogVisible = ref(false)

const textArray = ref(['是', '否', '是', '否'])
const print = () => {
  textArray.value = multilineText.value.split('\n').filter(line => line.trim() !== '')
  console.log(textArray.value);
}

const changeArray = (value) => {
  name.value = value.name
  textArray.value = value.options
  multilineText.value = textArray.value.join('\n')

}

const save = () => {
  optionsBtn.value.push({ name: name.value, options: textArray.value })
  dialogVisible.value = false
}

const deleteItem = () => {
  optionsBtn.value = optionsBtn.value.filter(item => item.name !== name.value)
  dialogVisible.value = false
}

const confettiVisible = ref(false)
const spin = () => {
  setTimeout(() => {
    confettiVisible.value = true
    setTimeout(() => {
      confettiVisible.value = false
    }, 3000);
  }, 3000);
}
</script>

<style lang="scss" scoped>
.justify-center{
  display: flex;
  justify-content: center;
}
</style>