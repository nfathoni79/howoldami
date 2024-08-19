<script setup>
import { computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const nowDate = new Date().toISOString().split('T')[0]

const birthDate = computed({
  get: () => {
    let year = route.query.y
    let month = route.query.m
    let day = route.query.d

    if (!year || !month || !day) {
      const date = new Date()
      year = date.getFullYear()
      month = date.getMonth() + 1
      day = date.getDate()
    }

    return `${year}-${month?.toString().padStart(2, '0')}-${day?.toString().padStart(2, '0')}`
  },
  set: newValue => {
    const date = new Date(newValue)

    router.replace({
      query: {
        y: date.getFullYear(),
        m: date.getMonth() + 1,
        d: date.getDate(),
      },
    })
  }
})

const age = computed(() => {
  const now = new Date()
  const birth = new Date(birthDate.value)

  if (now < birth) return null

  const nowDay = now.getDate()
  let nowMonth = now.getMonth() + 1
  let nowYear = now.getFullYear()
  const birthDay = birth.getDate()
  const birthMonth = birth.getMonth() + 1
  const birthYear = birth.getFullYear()

  let ageDay = 0

  if (nowDay < birthDay) {
    nowMonth -= 1
    ageDay = nowDay + 31 - birthDay
  } else {
    ageDay = nowDay - birthDay
  }

  let ageMonth = 0

  if (nowMonth < birthMonth) {
    nowYear -= 1
    ageMonth = nowMonth + 12 - birthMonth
  } else {
    ageMonth = nowMonth - birthMonth
  }

  const ageYear = nowYear - birthYear

  return {
    year: ageYear,
    month: ageMonth,
    day: ageDay,
  }
})

const currentYear = new Date().getFullYear()
</script>

<template>
  <div class="mx-auto max-w-sm min-h-screen
    flex flex-col justify-center gap-8 px-4 py-8 text-center text-gray-800">
    <input type="date" :max="nowDate" v-model="birthDate"
      class="block mx-auto text-center
      focus:border-green-500 focus:ring-green-500" />

    <div v-if="age" class="flex flex-col gap-4">
      <p class="text-4xl font-semibold uppercase">I am</p>
      <p class="text-8xl font-bold">{{ age.year }}</p>
      <p class="text-4xl font-semibold uppercase">years</p>
      <p class="font-medium uppercase">
        {{ age.month }} months {{ age.day }} days old
      </p>
    </div>
    <div v-else>
      <p>Birth date needs to be earlier than today</p>
    </div>

    <p>
      {{ `&copy; ${currentYear} by ` }}
      <a href="https://github.com/nfathoni79" target="_blank"
        class="text-green-600 hover:text-green-700 hover:underline">
        NN
      </a>
    </p>
  </div>
</template>
