<template>
  <div class="antialiased h-screen bg-gray-300">
    <header class="px-8 pt-6 flex items-center justify-between">
      <h1 class="font-medium text-white text-lg bg-gray-800 rounded px-3 py-1">
        code-fmt<span class="text-gray-600">.com</span>
      </h1>
      <language-picker />
    </header>
    <nuxt />
    <footer class="bg-white px-8 flex text-sm h-16">
      <a
        href="//github.com/thijsw/code-fmt"
        class="flex block items-center w-full h-16 opacity-50 hover:opacity-100 transition duration-200"
      >
        <img class="h-4 w-4" src="~/assets/github.png" alt="GitHub icon" />
        <span class="ml-2">Contribute on GitHub</span>
      </a>
    </footer>
  </div>
</template>

<script>
import LanguagePicker from '~/components/LanguagePicker'

export default {
  components: {
    LanguagePicker
  }
}
</script>
