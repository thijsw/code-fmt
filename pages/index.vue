<template>
  <div class="bg-gray-300 p-8">
    <header>
      <h1 class="font-medium text-gray-800 text-lg">
        code-format<span class="text-gray-600">.com</span>
      </h1>
      <h2 class="text-gray-700">
        Format source code using Prettier
      </h2>
    </header>
    <div class="flex mt-6 flex-1 max h-full text-sm">
      <textarea
        v-model="input"
        class="w-1/2 rounded-lg h-full focus:outline-none bg-gray-100 py-5 px-6 font-mono mr-2 focus:shadow-outline"
      ></textarea>
      <div
        class="relative w-1/2 rounded-lg text-white bg-gray-600 py-5 px-6 whitespace-pre font-mono ml-2 focus:shadow-outline overflow-scroll"
      >
        <div>{{ output }}</div>
        <div
          :class="{ 'opacity-0': !error, 'opacity-75': error }"
          class="bg-gray-900 inset-0 absolute py-5 px-6 text-red-600 transition-all duration-150 font-semibold pointer-events-none"
        >
          {{ error }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { format } from 'prettier/standalone'
import * as babylon from 'prettier/parser-babylon'

export default {
  data() {
    return {
      input: '',
      output: '',
      error: null
    }
  },

  watch: {
    input() {
      try {
        this.output = format(this.input, {
          parser: 'babel',
          plugins: [babylon]
        })
        this.error = null
      } catch (error) {
        this.error = error
      }
    }
  }
}
</script>

<style>
.max {
  max-height: calc(100% - 6rem);
}
</style>
