<template>
  <div class="rounded-lg h-full bg-gray-100">
    <div
      ref="lines"
      class="w-12 pr-4 text-gray-500 text-right overflow-hidden font-mono"
    >
      <ol>
        <li v-for="line in lines" :key="line">{{ line }}</li>
      </ol>
    </div>
    <textarea
      ref="textarea"
      :value="value"
      spellcheck="false"
      class="resize-none focus:outline-none w-full h-full bg-transparent font-mono whitespace-pre overflow-scroll"
      @input="$emit('input', $event.target.value)"
    ></textarea>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      required: true
    }
  },

  computed: {
    lines() {
      const matches = this.value.match(/\n/g)

      return (matches && matches.length) + 1
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
  }
}
</script>
