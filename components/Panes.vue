<template>
  <div class="flex p-8 flex-1 max h-full text-sm">
    <input-pane
      :value="value"
      class="flex w-1/2 py-5 px-4 mr-2"
      @input="(input) => $emit('input', input)"
    />
    <output-pane
      :output="output"
      :language="language"
      :error="error"
      class="flex w-1/2 py-5 pl-4 pr-6 ml-2"
    />
  </div>
</template>

<script>
import OutputPane from '~/components/OutputPane'
import InputPane from '~/components/InputPane'

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

<style>
.max {
  max-height: calc(100% - 7.5rem);
}
</style>
