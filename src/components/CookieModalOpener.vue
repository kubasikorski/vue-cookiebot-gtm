<template>
  <ul>
    <li class="emp">
      <a href="#" @click.prevent="openCookieModal()">Personalizacja cookies</a>
    </li>
  </ul>
</template>

<script>

export default {
  name: 'CookieModalOpener',

  methods: {
    openCookieModal(){
      this.$root.$emit("openCookieModal")
    }
  }
}
</script>