<template>
  <panes v-model="input" language="sql" :output="output" />
</template>

<script>
import sqlFormatter from 'sql-formatter'
import Panes from '~/components/Panes'

export default {
  components: {
    Panes
  },

  asyncData({ req }) {
    return {
      input: req ? '' : localStorage.getItem('input.sql') || ''
    }
  },

  fetchOnServer: false,

  data() {
    return {
      output: '',
      error: null
    }
  },

  watch: {
    input() {
      localStorage.setItem('input.sql', this.input)

      try {
        this.output = sqlFormatter.format(this.input)
        this.error = null
      } catch (error) {
        this.error = error
      }
    }
  }
}
</script>
