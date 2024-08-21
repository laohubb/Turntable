<script setup>
import { ref } from 'vue'
import { useStorage } from '@vueuse/core'

const optionsBtn = useStorage('options', [
  {
    name: '是/否',
    options: ['是', '否', '是', '否']
  }
])

const name = ref('')
const multilineText = ref('')


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
}
const deleteItem = () => {

  optionsBtn.value = optionsBtn.value.filter(item => item.name !== name.value)
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



<template>




  <v-sheet :elevation="4" class="fixed top-20 left-20" :width="300">
    <div class="mt-4 mb-4">
      <v-chip v-for="item in optionsBtn" :key="item.name" @click="changeArray(item)">{{ item.name }}</v-chip>

    </div>
    <v-text-field label="分类" v-model="name"></v-text-field>
    <v-textarea v-model="multilineText" @change="print" label="选项" rows="5" outlined></v-textarea>
    <div class="flex">
      <v-btn class="" @click="deleteItem">删除</v-btn>
      <v-btn @click="save" class="mb-1 ml-1">保存</v-btn>

    </div>


  </v-sheet>


  <WheelPanel class="mt-20" :sectors="textArray" @spin="spin" />
  <ConfettiDemo v-if="confettiVisible" class="confetti"></ConfettiDemo>




</template>

<style>
.confetti {}
</style>
