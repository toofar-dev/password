<template>
  <div class="flex justify-center items-center w-screen h-screen">
    <div class="space-y-6 w-92 h-max">
      <h1 class="text-2xl font-semibold text-center font-display">Password Generator</h1>
      <div class="grid grid-cols-2 gap-4">
        <UCheckbox v-model="includeUppercase" label="Uppercase Letters" />
        <UCheckbox v-model="includeLowercase" label="Lowercase Letters" />
        <UCheckbox v-model="includeNumbers" label="Numbers" />
        <UCheckbox v-model="includeSymbols" label="Symbols" />
      </div>
      <USlider v-model="length" :min="4" :max="32" :step="1" size="sm" tooltip />
      <UCard variant="soft">
        <div class="flex justify-between items-center">
          <p class="font-mono text-sm">{{ password }}</p>
          <UButton
            icon="lucide:copy"
            variant="soft"
            size="sm"
            @click="onCopy"
            :disabled="!password"
          />
        </div>
      </UCard>
      <UButton label="Generate" icon="lucide:wand" color="primary" block @click="generate" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const toast = useToast()

const password = ref<string>('')
const length = ref<number>(16)
const includeUppercase = ref<boolean>(true)
const includeLowercase = ref<boolean>(true)
const includeNumbers = ref<boolean>(true)
const includeSymbols = ref<boolean>(false)

const generate = (): void => {
  const uppercaseChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
  const lowercaseChars = 'abcdefghijklmnopqrstuvwxyz'
  const numberChars = '0123456789'
  const symbolChars = '!@#$%^&*()_+-=[]{}|;:,.<>?'

  let chars = ''
  if (includeUppercase.value) chars += uppercaseChars
  if (includeLowercase.value) chars += lowercaseChars
  if (includeNumbers.value) chars += numberChars
  if (includeSymbols.value) chars += symbolChars

  if (!chars) {
    chars = lowercaseChars
    includeLowercase.value = true
  }

  let generatedPassword = ''
  for (let i = 0; i < length.value; i++) {
    const randomIndex = Math.floor(Math.random() * chars.length)
    generatedPassword += chars[randomIndex]
  }

  password.value = generatedPassword
}

const onCopy = async (): Promise<void> => {
  if (!password.value) return
  navigator.clipboard
    .writeText(password.value)
    .then(() => {
      toast.add({
        title: 'Password copied',
        icon: 'lucide:check',
        color: 'success',
      })
    })
    .catch(() => {
      toast.add({
        title: 'Failed to copy password',
        icon: 'lucide:badge-alert',
        color: 'error',
      })
    })
}

onMounted(() => {
  generate()
})
</script>
