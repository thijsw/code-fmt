<template>
  <div class="bg-gray-300">
    <header class="px-8 pt-6 flex items-center justify-between">
      <h1 class="font-medium text-white text-lg bg-gray-800 rounded px-3 py-1">
        code-fmt<span class="text-gray-600">.com</span>
      </h1>
      <language-picker />
    </header>
    <div class="flex p-8 flex-1 max h-full text-sm">
      <input-pane v-model="input" class="flex w-1/2 py-5 px-4 mr-2" />
      <output-pane
        :value="output"
        :language="language"
        :error="error"
        class="flex w-1/2 py-5 pl-4 pr-6 ml-2"
      />
    </div>
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
import { format } from 'prettier/standalone'
import * as babel from 'prettier/parser-babylon'
import phpPlugin from '@prettier/plugin-php/standalone'
import sqlFormatter from 'sql-formatter'

import LanguagePicker from '~/components/LanguagePicker'
import InputPane from '~/components/InputPane'
import OutputPane from '~/components/OutputPane'

export default {
  components: {
    OutputPane,
    InputPane,
    LanguagePicker
  },

  validate({ params }) {
    const { language } = params

    return language === 'sql' || language === 'php' || !language
  },

  fetch() {
    const key = `input.${this.language}`
    const input = localStorage.getItem(key)
    if (input) {
      this.$nextTick(() => (this.input = input))
    }
  },

  fetchOnServer: false,

  data() {
    return {
      input: '',
      output: '',
      error: null
    }
  },

  computed: {
    language() {
      const { language } = this.$route.params

      switch (language) {
        case 'php':
        case 'sql':
          return language
        default:
          return 'babel'
      }
    },

    plugin() {
      if (this.language === 'babel') {
        return babel
      }

      if (this.language === 'php') {
        return phpPlugin
      }

      return null
    }
  },

  watch: {
    language(lang) {
      this.input = localStorage.getItem(`input.${lang}`)
    },

    input() {
      const key = `input.${this.language}`
      localStorage.setItem(key, this.input)

      try {
        if (this.language === 'sql') {
          this.output = sqlFormatter.format(this.input)
          return
        }

        this.output = format(this.input, {
          parser: this.language,
          plugins: [this.plugin]
        })

        this.error = null
      } catch (error) {
        this.error = error
      }
    }
  },

  head() {
    let language = ''
    switch (this.language) {
      case 'babel':
        language = 'Javascript ES6 Babel'
        break
      case 'php':
        language = 'PHP'
        break
      case 'sql':
        language = 'SQL'
    }

    return {
      title: `Format ${language} code using Prettier online`,
      meta: [
        {
          hid: 'index',
          name: 'description',
          content: `Format and beautify ${language} source code in an online editor`
        }
      ]
    }
  }
}
</script>

<style>
.max {
  max-height: calc(100% - 7.5rem);
}
</style>
