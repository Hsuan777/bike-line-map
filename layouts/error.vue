<template>
  <div>
    <p v-if="error.statusCode === 404">
      Page not found
    </p>
    <NuxtLink to="/">
      Go HOME
    </NuxtLink>
  </div>
</template>
<script>
export default {
  props: {
    error: {
      type: Object,
      default: null
    }
  }
}
</script>
