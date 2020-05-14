<template>
  <div class="bg-gray-300 p-8">
    <header>
      <h1 class="font-medium text-gray-800 text-lg">
        code-fmt<span class="text-gray-600">.com</span>
      </h1>
      <h2 class="text-gray-700">
        Format source code using Prettier
      </h2>
    </header>
    <section class="flex justify-end hidden">
      <ul class="flex">
        <li>
          <nuxt-link
            to="/mysql"
            class="block hover:bg-gray-400 transition duration-150 rounded px-2 py-1 text-gray-800"
          >
            mysql
          </nuxt-link>
        </li>
        <li>
          <nuxt-link
            to="/"
            class="ml-2 block rounded px-2 py-1 bg-yellow-600 text-white"
          >
            js/babel
          </nuxt-link>
        </li>
        <li>
          <nuxt-link
            to="/php"
            class="ml-2 block rounded px-2 py-1 bg-purple-600 text-white"
          >
            php
          </nuxt-link>
        </li>
      </ul>
    </section>
    <div class="flex mt-6 flex-1 max h-full text-sm">
      <div
        ref="input"
        class="flex w-1/2 rounded-lg h-full bg-gray-100 py-5 pl-4 pr-6 whitespace-pre font-mono mr-2 focus:shadow-outline overflow-scroll"
      >
        <div class="w-12 pr-4 text-gray-500 text-right">
          <ol>
            <li v-for="n in inputLines" :key="n">{{ n }}</li>
          </ol>
        </div>
        <textarea
          v-model="input"
          spellcheck="false"
          class="resize-none focus:outline-none w-full h-full bg-transparent"
        ></textarea>
      </div>
      <div
        ref="output"
        class="relative flex w-1/2 rounded-lg text-white bg-gray-800 py-5 pl-4 pr-6 whitespace-pre font-mono ml-2 focus:shadow-outline overflow-scroll"
      >
        <div class="w-12 pr-4 text-gray-500 text-right">
          <ol>
            <li v-for="n in outputLines" :key="n">{{ n }}</li>
          </ol>
        </div>
        <div v-html="highlighted" />
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
import phpPlugin from '@prettier/plugin-php/standalone'
import highlight from 'highlight.js/lib/core'
import js from 'highlight.js/lib/languages/javascript'
import php from 'highlight.js/lib/languages/php'

highlight.registerLanguage('babylon', js)
highlight.registerLanguage('php', php)

export default {
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
          return 'php'
        default:
          return 'babylon'
      }
    },

    plugin() {
      if (this.language === 'babylon') {
        return babylon
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
    input() {
      try {
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

  head() {
    return {
      title: 'Format Javascript ES6 Babel code using Prettier online'
    }
  }
}
</script>

<style>
.max {
  max-height: calc(100% - 6rem);
}
</style>
