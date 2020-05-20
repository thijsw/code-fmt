<template>
  <panes v-model="input" :output="output" :language="language" :error="error" />
</template>

<script>
import { format } from 'prettier/standalone'
import * as babel from 'prettier/parser-babylon'
import phpPlugin from '@prettier/plugin-php/standalone'
import sqlFormatter from 'sql-formatter'

import Panes from '~/components/Panes'

export default {
  components: {
    Panes
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
