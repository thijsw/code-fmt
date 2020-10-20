<template>
  <div class="antialiased h-screen bg-gray-300 flex flex-col">
    <header class="px-8 py-4 flex items-center justify-between">
      <h1 class="font-medium text-white text-lg bg-gray-800 rounded px-3 py-1">
        code-fmt<span class="text-gray-600">.com</span>
      </h1>
      <language-picker />
    </header>
    <nuxt class="flex-1" />
    <footer class="px-8 flex text-sm h-16 absolute bottom-0">
      <a
        href="//github.com/thijsw/code-fmt"
        class="flex block items-center w-full h-16 opacity-75 text-white hover:opacity-100 transition duration-200"
      >
        <img class="h-6 w-6" src="~/assets/github.png" alt="GitHub icon" />
        <span class="ml-4">Contribute on GitHub</span>
      </a>
    </footer>
  </div>
</template>

<script>
import LanguagePicker from '~/components/LanguagePicker'

export default {
  components: {
    LanguagePicker
  },

  head: {
    bodyAttrs: {
      class: 'fixed overflow-hidden w-full min-h-full'
    }
  }
}
</script>
