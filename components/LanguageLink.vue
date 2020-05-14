<template>
  <nuxt-link
    :class="{
      [`text-white border-${color}-700 bg-${color}-600`]: $route.path === to,
      [`hover:border-${color}-600 text-gray-800 border-gray-400 bg-gray-200`]:
        $route.path !== to
    }"
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
  }
}
</script>
