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
import { computed, onMounted, ref } from 'vue'
import { MainButton } from 'vue-tg'
import { useWebAppPopup, useWebAppHapticFeedback, useWebAppMainButton } from 'vue-tg'
import { useWebApp } from 'vue-tg'
import { useWebAppViewport } from 'vue-tg'

const { close, initDataUnsafe } = useWebApp()
const { hideMainButtonProgress, showMainButtonProgress } = useWebAppMainButton()
const { showAlert, showConfirm } = useWebAppPopup()
const { impactOccurred } = useWebAppHapticFeedback()
const { expand } = useWebAppViewport()


const coins = ref(0)
const scale = ref('scale-100')
const text = computed(() => coins.value === 0 ? 'Закрыть' : 'Вывести коины')


const createDeposit = async ({ userId, amount, description }) => {
  showMainButtonProgress?.()
  const url = `${import.meta.env.VITE_API_URL}/deposit`
  const options = {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ user_id: userId, amount, description }),
  }
  try {
    await fetch(url, options)
    coins.value = 0
    showAlert?.('Коины успешно выведены!')
  } catch (error) {
    showAlert?.(`Ошибка! Попробуйте позже! ${error}`)
  } finally {
    hideMainButtonProgress?.()
  }
}

const onMainButtonClick = async () => {
  if (coins.value === 0) {
    close?.()
    return
  }
  const userId = initDataUnsafe?.user?.id
  if (!userId) {
    showAlert?.('Ошибка! Пользователь не найден!')
    return
  }
  await createDeposit({ userId, amount: coins.value, description: 'Кликер' })
}

const getRandomValueBetween = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1) + min)
}

const onClick = () => {
  impactOccurred?.('light')
  scale.value = 'scale-75'
  setTimeout(() => {
    scale.value = 'scale-100'
    coins.value += getRandomValueBetween(30, 60)
  }, 75)
}

onMounted(() => {
  expand?.()
})
</script>
