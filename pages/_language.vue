<template>
  <div class="bg-gray-300">
    <header class="px-8 pt-6 flex items-center justify-between">
      <h1 class="font-medium text-white text-lg bg-gray-800 rounded px-3 py-1">
        code-fmt<span class="text-gray-600">.com</span>
      </h1>
      <language-picker />
    </header>
    <div class="flex p-8 flex-1 max h-full text-sm">
      <div
        ref="input"
        class="flex w-1/2 rounded-lg h-full bg-gray-100 py-5 px-4 mr-2"
      >
        <div
          ref="lines"
          class="w-12 pr-4 text-gray-500 text-right overflow-hidden font-mono"
        >
          <ol>
            <li v-for="n in inputLines" :key="n">{{ n }}</li>
          </ol>
        </div>
        <textarea
          ref="textarea"
          v-model="input"
          spellcheck="false"
          class="resize-none focus:outline-none w-full h-full bg-transparent font-mono whitespace-pre overflow-scroll"
        ></textarea>
      </div>
      <div
        ref="output"
        :class="{ 'overflow-scroll': !error, 'overflow-hidden': error }"
        class="relative flex w-1/2 rounded-lg text-white bg-gray-800 py-5 pl-4 pr-6 whitespace-pre font-mono ml-2 focus:shadow-outline"
      >
        <button
          class="absolute top-0 right-0 pt-5 pr-4 focus:outline-none"
          @click.prevent="copy"
        >
          <svg
            class="transition duration-150 text-gray-500 w-8 h-8 hover:text-gray-200"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3"
            />
          </svg>
        </button>
        <div class="w-12 pr-4 text-gray-500 text-right">
          <ol>
            <li v-for="n in outputLines" :key="n">{{ n }}</li>
          </ol>
        </div>
        <!-- eslint-disable-next-line -->
        <div v-html="highlighted" />
        <div
          :class="{
            'opacity-0 pointer-events-none': !error,
            'opacity-75 overflow-scroll': error
          }"
          class="bg-gray-900 inset-0 absolute py-5 px-6 text-red-600 transition-all duration-150 font-semibold"
        >
          {{ error }}
        </div>
      </div>
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
import highlight from 'highlight.js/lib/core'
import sqlFormatter from 'sql-formatter'
import clipboardCopy from 'clipboard-copy'

import js from 'highlight.js/lib/languages/javascript'
import php from 'highlight.js/lib/languages/php'
import sql from 'highlight.js/lib/languages/sql'
import LanguagePicker from '~/components/LanguagePicker'

highlight.registerLanguage('babel', js)
highlight.registerLanguage('php', php)
highlight.registerLanguage('sql', sql)

export default {
  components: {
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
    },

    highlighted() {
      return highlight.highlight(this.language, this.output, true, null).value
    },

    inputLines() {
      const matches = this.input.match(/\n/g)

      return (matches && matches.length) + 1
    },

    outputLines() {
      const matches = this.output.match(/\n/g)

      return (matches && matches.length) + 1
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
        // Scroll to top
        this.$refs.output.scrollTop = 0

        this.error = error
      }
    }
  },

  created() {
    // Sync line numbers with textarea
    this.$nextTick(() => {
      if (!this.$refs.textarea) {
        return
      }
      this.$refs.textarea.addEventListener('scroll', (event) => {
        if (!this.$refs.lines) {
          return
        }
        this.$refs.lines.scrollTop = event.target.scrollTop
      })
    })
  },

  methods: {
    async copy() {
      await clipboardCopy(this.output)
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
@import 'highlight.js/styles/atom-one-dark.css';

.max {
  max-height: calc(100% - 7.5rem);
}
</style>
