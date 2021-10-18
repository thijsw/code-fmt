<template>
  <div class="flex">
    <client-only>
      <div class="w-4/5 relative">
        <prism-editor
          v-model="input"
          :language="language"
          line-numbers
          :highlight="highlight"
          class="input-editor"
          @input="(code) => $emit('input', code)"
        />
        <aside v-if="error" class="space-x-4 flex rounded bg-gray-200 px-4 py-3 absolute bottom-4 right-4 max-w-lg shadow">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 my-1 flex-shrink-0 text-red-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          <p class="text-gray-900">{{ error }}</p>
        </aside>
      </div>
    </client-only>
    <div class="w-1/5 bg-gray-700 p-4">
      <textarea v-model="configuration" placeholder="JSON configuration" :class="{ 'border-transparent': config, 'border-red-400': !config }" class="p-2 bg-gray-900 text-sm text-white focus:bg-gray-900 rounded font-mono w-full h-48 focus:outline-none border-2 transition-colors duration-200"></textarea>
      <button class="mt-2 bg-blue-600 text-white font-semibold px-3 py-1 rounded hover:bg-blue-500 transition-colors duration-150" @click.prevent="parse">Format</button>
    </div>
  </div>
</template>

<script>
import { PrismEditor } from 'vue-prism-editor'
import 'vue-prism-editor/dist/prismeditor.min.css'

import { highlight as prism, languages } from 'prismjs/components/prism-core'
import { format } from 'prettier/standalone'

import 'prismjs/components/prism-markup-templating'
import 'prismjs/components/prism-clike'
import 'prismjs/components/prism-javascript'
import 'prismjs/components/prism-php'
import 'prismjs/components/prism-sql'

import 'prismjs/themes/prism-tomorrow.css'

export default {
  components: {
    PrismEditor
  },

  validate({ params }) {
    const { language } = params

    return language === 'sql' || language === 'php' || !language
  },

  data() {
    return {
      input: '',
      error: null,
      plugin: null,
      configuration: ''
    }
  },

  fetch() {
    const configuration = localStorage.getItem(`configuration.${this.language}`)
    const defaultConfiguration = this.language === 'sql' ?
      {
        language: 'sql',
        indent: '  ',
        uppercase: true,
        linesBetweenQueries: 1
      } : {
        trailingComma: 'es5',
        tabWidth: 2,
        semi: false,
        singleQuote: true
      }

    this.configuration = configuration || JSON.stringify(defaultConfiguration, null, 2)

    this.$nextTick(() => {
      this.input = localStorage.getItem(`input.${this.language}`) || ''
    })
  },

  fetchOnServer: false,

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
    },

    config() {
      try {
        return JSON.parse(this.configuration)
      } catch {
        return null
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
      },
      immediate: true
    },

    input() {
      localStorage.setItem(`input.${this.language}`, this.input)
    },

    configuration() {
      localStorage.setItem(`configuration.${this.language}`, this.configuration)
    }
  },

  methods: {
    highlight(code) {
      return prism(code, languages[this.language])
    },

    parse() {
      if (!this.plugin) {
        return
      }

      try {
        const config = this.config || {}

        if (this.language === 'sql') {
          this.input = this.plugin.format(this.input, config)
          return
        }

        this.input = format(this.input, {
          ...config,
          parser: this.language === 'javascript' ? 'babel' : this.language,
          plugins: [this.plugin]
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
/* purgecss start ignore */
.input-editor {
  @apply font-mono text-sm leading-6 p-2 text-gray-100 bg-gray-900;
}

.input-editor .prism-editor__container {
  @apply h-full;
}

.prism-editor__textarea:focus {
  @apply outline-none;
}
/* purgecss end ignore */
</style>
