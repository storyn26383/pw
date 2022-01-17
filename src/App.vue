<template>
  <div class="container mx-auto py-6">
    <form @submit.prevent="handleSubmit">
      <div class="shadow overflow-hidden sm:rounded-md">
        <div class="px-4 py-5 space-y-6 bg-white sm:p-6">
          <div>
            <label for="password">Password</label>
            <input id="password" type="password" v-model="password" ref="passwordInput">
          </div>
          <div>
            <label for="salt">Salt</label>
            <input id="salt" type="password" v-model="salt">
          </div>
        </div>
        <div class="px-4 py-3 bg-gray-50 text-right sm:px-6">
          <button type="submit">Calculate</button>
        </div>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
  import { ref, computed, nextTick } from 'vue'
  import sha256 from 'crypto-js/sha256'

  const ASCII_START = 33  // !
  const ASCII_END   = 126 // ~

  const password = ref('')
  const salt = ref('')
  const passwordInput = ref<HTMLInputElement>()

  async function handleSubmit() {
    const hashed = sha256(`${password.value}${salt.value}`).toString()
    const bytes: number[] = hashed.match(/.{1,2}/g)!.map((hex: string) => parseInt(hex, 16))
    const hashedPassword = bytes.map((byte, index) => String.fromCharCode((byte * index) % (ASCII_END - ASCII_START + 1) + ASCII_START)).join('')

    await navigator.clipboard.writeText(hashedPassword)

    password.value = ''
    salt.value = ''
    passwordInput.value!.focus()
  }

  nextTick(() => passwordInput.value!.focus())
</script>

<style scoped>
  label {
    @apply block mb-1 font-medium text-gray-700;
  }

  input {
    @apply block px-3 py-2 w-full border border-gray-300 shadow-sm rounded-md focus:ring-indigo-500 focus:border-indigo-500;
  }

  button {
    @apply inline-flex justify-center py-2 px-4 border border-transparent shadow-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500;
  }
</style>
