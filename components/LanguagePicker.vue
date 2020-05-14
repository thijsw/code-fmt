<template>
  <section class="flex justify-end">
    <ul class="flex">
      <li>
        <language-link to="/" color="yellow">
          javascript (babel)
        </language-link>
      </li>
      <li class="ml-2">
        <language-link to="/php" color="purple">
          php
        </language-link>
      </li>
      <li class="ml-2">
        <language-link to="/sql" color="blue">
          sql
        </language-link>
      </li>
    </ul>
  </section>
</template>

<script>
import LanguageLink from '~/components/LanguageLink'

export default {
  components: {
    LanguageLink
  }
}
</script>
