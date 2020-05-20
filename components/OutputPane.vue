<template>
  <div
    ref="output"
    :class="{ 'overflow-scroll': !error, 'overflow-hidden': error }"
    class="relative rounded-lg text-white bg-gray-800 whitespace-pre font-mono focus:shadow-outline"
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
          stroke-width="1.5"
          d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3"
        />
      </svg>
    </button>
    <div class="w-12 pr-4 text-gray-500 text-right">
      <ol>
        <li v-for="line in lines" :key="line">{{ line }}</li>
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
</template>

<script>
import clipboardCopy from 'clipboard-copy'

import 'highlight.js/styles/atom-one-dark.css'

import highlight from 'highlight.js/lib/core'
import js from 'highlight.js/lib/languages/javascript'
import php from 'highlight.js/lib/languages/php'
import sql from 'highlight.js/lib/languages/sql'

highlight.registerLanguage('babel', js)
highlight.registerLanguage('php', php)
highlight.registerLanguage('sql', sql)

export default {
  props: {
    value: {
      type: String,
      required: true
    },

    language: {
      type: String,
      required: true,
      validator(value) {
        return ['babel', 'php', 'sql'].includes(value)
      }
    },

    error: {
      type: Error,
      required: false,
      default: null
    }
  },

  computed: {
    highlighted() {
      return highlight.highlight(this.language, this.value, true, null).value
    },

    lines() {
      const matches = this.value.match(/\n/g)

      return (matches && matches.length) + 1
    }
  },

  watch: {
    error() {
      // Scroll to top when a new error arrives
      if (this.error) {
        this.$refs.output.scrollTop = 0
      }
    }
  },

  methods: {
    async copy() {
      await clipboardCopy(this.value)
    }
  }
}
</script>
