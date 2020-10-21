<template>
  <div>
    <client-only>
      <prism-editor
        :value="value"
        line-numbers
        :highlight="highlight"
        class="input-editor"
        @input="(code) => $emit('input', code)"
      />
    </client-only>
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
    value: {
      type: String,
      required: true
    },

    language: {
      type: String,
      required: true,
      validator(value) {
        return ['javascript', 'php', 'sql'].includes(value)
      }
    }
  },

  methods: {
    highlight(code) {
      return prism(code, languages[this.language])
    }
  }
}
</script>

<style>
.input-editor {
  @apply font-mono text-sm leading-6 p-2 text-gray-100 bg-gray-900;
}

.prism-editor__textarea:focus {
  @apply outline-none;
}
</style>
