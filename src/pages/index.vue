<script setup>
import { ref } from 'vue'
import { useStorage } from '@vueuse/core'

const optionsBtn = useStorage('options', [
    {
        name: '是/否',
        options:['是', '否', '是', '否']
    }
])

const name=ref('')
const multilineText = ref('')


const textArray = ref(['是', '否', '是', '否'])
const print = () => {
  textArray.value = multilineText.value.split('\n').filter(line => line.trim() !== '')
  console.log(textArray.value);
  
}

const changeArray = (value) => {
  name.value=value.name
  textArray.value = value.options
  multilineText.value = textArray.value.join('\n')
}

const save = () => {
  optionsBtn.value.push({name: name.value, options: textArray.value})
}
const deleteItem = () => {
  
  optionsBtn.value = optionsBtn.value.filter(item => item.name !== name.value)
}
</script>



<template>

  <v-container>
    <v-row>
      <v-col>
        <Wheel :sectors="textArray" />
      </v-col>
    </v-row>
 
 
    <v-row>
      <v-col cols="6">
        <v-text-field label="分类" v-model="name"></v-text-field>

        <v-textarea v-model="multilineText" @change="print" label="Multiline Text Input" rows="5" outlined></v-textarea>

      </v-col>
      <v-col cols="1"  v-if="name">
       <v-btn @click="save" class="mb-1">保存</v-btn>
       <v-btn @click="deleteItem">删除</v-btn>

      </v-col>
      <v-col>

        <v-btn v-for="item in optionsBtn" :key="item.name" @click="changeArray(item)">{{ item.name }}</v-btn>
      </v-col>
    </v-row>

  </v-container>


</template>
