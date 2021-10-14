<template>
  <panes v-model="input" :output="output" :language="language" :error="error" />
</template>

<script>
import 'vue-prism-editor/dist/prismeditor.min.css'
import { format } from 'prettier/standalone'
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
      error: null,
      plugin: null
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
          return 'javascript'
      }
    }
  },

  watch: {
    language: {
      async handler() {
        const lang = this.language

        if (lang === 'javascript') {
          this.plugin = await import('prettier/parser-babel')
        }

        if (lang === 'php') {
          this.plugin = await import('@prettier/plugin-php/standalone')
        }

        if (lang === 'sql') {
          this.plugin = await import('sql-formatter')
        }

        this.parse()
      },
      immediate: true
    },

    input() {
      const key = `input.${this.language}`
      localStorage.setItem(key, this.input)

      this.parse()
    }
  },

  methods: {
    parse() {
      if (!this.plugin) {
        return
      }

      try {
        if (this.language === 'sql') {
          this.output = this.plugin.format(this.input)
          return
        }

        this.output = format(this.input, {
          parser: this.language === 'javascript' ? 'babel' : this.language,
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
      case 'javascript':
        language = 'Modern Javascript'
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
