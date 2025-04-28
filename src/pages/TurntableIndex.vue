<template>

  <v-dialog v-model="dialogVisible" max-width="400">

    <v-card>
      <v-card-title>
        <span class="headline">编辑选项</span>
      </v-card-title>
      <v-card-text>
        <v-text-field label="分类" v-model="name"></v-text-field>
        <v-textarea v-model="multilineText" @input="print" label="选项" rows="5" outlined></v-textarea>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn @click="deleteItem">删除</v-btn>
        <v-btn @click="save">保存</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
  <div style="width: 100%;min-height: 450px;">
    <WheelPanel class="mt-10" :sectors="textArray" @spin="spin" />

  </div>
  <v-sheet class="px-2 w-full mt-2" :class="{ 'justify-center': !isMobile }">
    <v-chip v-for="item in optionsBtn" :key="item.name" @click="changeArray(item)">{{ item.name }}</v-chip>
    <v-btn variant="text" size="x-small" icon="mdi-plus" @click="createNew"></v-btn>
    <v-btn variant="text" size="x-small" icon="mdi-pencil-outline" @click="openEdit"></v-btn>

  </v-sheet>
  <ConfettiDemo v-if="confettiVisible" class="confetti"></ConfettiDemo>
</template>

<script setup>
import { ref, onMounted } from 'vue'
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

// 保存当前选中的选项名称
const currentSelection = useStorage('currentSelection', '是/否')

const name = ref('')
const multilineText = ref('')
const dialogVisible = ref(false)
const isEditing = ref(false)

const textArray = ref(['是', '否', '是', '否'])

// 初始化时从存储加载上次选择的选项
onMounted(() => {
  const savedOption = optionsBtn.value.find(item => item.name === currentSelection.value)
  if (savedOption) {
    changeArray(savedOption)
  }
})

const print = () => {
  textArray.value = multilineText.value.split('\n').filter(line => line.trim() !== '')
}

const changeArray = (value) => {
  name.value = value.name
  textArray.value = JSON.parse(JSON.stringify(value.options))
  multilineText.value = textArray.value.join('\n')
  // 保存当前选择到本地存储
  currentSelection.value = value.name
}

const createNew = () => {
  isEditing.value = false
  name.value = ''
  textArray.value = ['选项1', '选项2']
  multilineText.value = '选项1\n选项2'
  dialogVisible.value = true
}

const openEdit = () => {
  isEditing.value = true
  dialogVisible.value = true
}

const save = () => {
  if (!name.value.trim()) {
    alert('请输入分类名称')
    return
  }

  if (textArray.value.length < 2) {
    alert('请至少添加两个选项')
    return
  }

  // 判断是否在编辑现有分类
  const existingIndex = optionsBtn.value.findIndex(item => item.name === name.value)

  if (existingIndex >= 0) {
    // 更新现有分类
    optionsBtn.value[existingIndex].options = [...textArray.value]
  } else {
    // 添加新分类
    optionsBtn.value.push({
      name: name.value,
      options: [...textArray.value]
    })
  }

  dialogVisible.value = false
}

const deleteItem = () => {
  if (!name.value.trim()) return

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
.justify-center {
  display: flex;
  justify-content: center;
}
</style>