<template lang="pug">
  section(class="container")
    div
      div(v-if="!timeout")
        countdown(ref="countdown" :time="20 * 1000", @countdownend="timeout = true")
          template(slot-scope="props") {{ props.minutes }}:{{ props.seconds }}

        form
          label Entrez le mot de passe
          input(v-model="password")
          button(@click.prevent="validatePassword") Valider
      div(v-else) YOU LOOSE !

    .debug
      button(@click.prevent="$refs.countdown.pause") DEBUG pause
      button(@click.prevent="$refs.countdown.start") DEBUG start

</template>

<script>
import Countdown from '@xkeshi/vue-countdown'

export default {
  components: {
    Countdown
  },
  data () {
    return {
      password: "",
      timeout: false
    }
  },
  methods: {
    validatePassword () {
      if (this.password == "yolo") {
        this.$refs.countdown.pause()
        alert("bravo!")

      } else {
        alert("raté !")
      }
    }
  }
}
</script>

<style scoped>
.debug {
  position: fixed;
  width: 100%;
  bottom: 0;
  background-color: grey;
}

section {
  text-align: center;
  height: 100%;
}
</style>
