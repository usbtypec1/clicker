<template>
  <div class="px-4 py-6 h-screen">
    <p class="text-white text-xl text-center">Заработано: {{ coins }} дак-дак коинов</p>
    <div class="h-full flex flex-col items-center justify-center">
      <img
        @click="onClick"
        class="rounded-full max-w-80 cursor-pointer transition-all text-amber-200 border-2 border-amber-200 shadow-[0_0_2px_#fff,inset_0_0_2px_#fff,0_0_5px_#ffa500,0_0_15px_#ffa500,0_0_30px_#ffa500]"
        :class="[scale]"
        src="https://i.imgur.com/hmgDUOm.jpeg"
      />
    </div>
    <MainButton :text="text" @click="onMainButtonClick"/>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import { MainButton } from 'vue-tg'
import { useWebAppPopup } from 'vue-tg'

const { showAlert } = useWebAppPopup()


const coins = ref(0)
const scale = ref('scale-100')
const text = computed(() => coins.value === 0 ? 'Закрыть' : 'Вывести коины')

const onMainButtonClick = () => {
  showAlert?.('Hello from Vue 3!')
}

const getRandomValueBetween = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1) + min)
}

const onClick = () => {
  scale.value = 'scale-75'
  setTimeout(() => {
    scale.value = 'scale-100'
    coins.value += getRandomValueBetween(1000000, 2000000)
  }, 50)
}
</script>
