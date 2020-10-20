<template>
  <div class="flex">
    <input-pane
      :value="value"
      :language="language"
      class="flex w-1/2"
      @input="(input) => $emit('input', input)"
    />
    <output-pane
      :output="output"
      :language="language"
      :error="error"
      class="flex w-1/2"
    />
  </div>
</template>

<script>
import OutputPane from '~/components/OutputPane'
import InputPane from '~/components/InputPane'
import 'vue-prism-editor/dist/prismeditor.min.css'
import 'prismjs/themes/prism-tomorrow.css'

import 'prismjs/components/prism-markup-templating'
import 'prismjs/components/prism-clike'
import 'prismjs/components/prism-javascript'
import 'prismjs/components/prism-php'
import 'prismjs/components/prism-sql'

export default {
  components: {
    OutputPane,
    InputPane
  },

  props: {
    value: {
      type: String,
      required: true
    },
    output: {
      type: String,
      required: true
    },
    language: {
      type: String,
      required: true
    },
    error: {
      type: Error,
      required: false,
      default: null
    }
  }
}
</script>
