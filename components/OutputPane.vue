<template>
  <div
    ref="output"
    :class="{ 'overflow-scroll': !error, 'overflow-hidden': error }"
    class="relative"
  >
    <client-only>
      <prism-editor
        :value="output"
        line-numbers
        readonly
        :highlight="highlight"
        class="output-editor"
      />
    </client-only>
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
import { PrismEditor } from 'vue-prism-editor'

import { highlight as prism, languages } from 'prismjs/components/prism-core'

export default {
  components: {
    PrismEditor
  },

  props: {
    output: {
      type: String,
      required: true
    },

    language: {
      type: String,
      required: true,
      validator(value) {
        return ['javascript', 'php', 'sql'].includes(value)
      }
    },

    error: {
      type: Error,
      required: false,
      default: null
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
    highlight() {
      return prism(this.output, languages[this.language])
    }
  }
}
</script>

<style>
/* purgecss start ignore */
.output-editor {
  @apply font-mono text-sm leading-6 p-2 text-gray-800 bg-gray-100;
}

.output-editor .token.punctuation {
  @apply text-gray-500 opacity-100;
}

.output-editor .token.selector,
.output-editor .token.important,
.output-editor .token.atrule,
.output-editor .token.keyword,
.output-editor .token.builtin {
  color: #d877d9;
}
/* purgecss end ignore */
</style>
