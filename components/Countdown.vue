<template lang="pug">
  span {{ count }}
</template>

<script>
  export default {
    data () {
      return {
        count: 30
      }
    }
  }
</script>

<style scoped lang="jade">
  span {
    font-size: 10em
  }
</style>