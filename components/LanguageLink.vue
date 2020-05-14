<template>
  <nuxt-link
    :class="classes"
    :to="to"
    class="block border-b-2 rounded px-3 py-1 duration-150 transition"
  >
    <slot />
  </nuxt-link>
</template>

<script>
export default {
  props: {
    to: {
      type: String,
      required: true
    },

    color: {
      type: String,
      required: true
    }
  },

  computed: {
    classes() {
      if (this.$route.path === this.to) {
        return `${this.color}-active active`
      }
      return `hover:border-${this.color}-600 ${this.color}`
    }
  }
}
</script>

<style scoped>
a {
  @apply text-gray-800 bg-gray-200;
}

a:not(:hover, .active) {
  @apply border-gray-400;
}

.active {
  @apply text-white;
}

.blue-active {
  @apply border-blue-700 bg-blue-600;
}

.purple-active {
  @apply border-purple-700 bg-purple-600;
}

.yellow-active {
  @apply border-yellow-700 bg-yellow-600;
}
</style>
