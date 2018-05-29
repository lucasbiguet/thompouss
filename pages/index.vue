<template lang="pug">
  section(class="container")
    .main
      div(v-if="!timeout")
        .countdown
          countdown(ref="countdown" :time="20 * 1000", @countdownend="timeout = true")
            template(slot-scope="props") {{ props.minutes }}:{{ props.seconds }}

        form
          label Entrez le mot de passe
          input(v-model="password")
          button(@click.prevent="validatePassword") Valider
      div(v-else) Perdu !

    .debug
      span Mode administrateur:
      button(@click.prevent="$refs.countdown.pause") pause
      button(@click.prevent="$refs.countdown.start") start

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
      if (this.password == "missionhandicap") {
        this.$refs.countdown.pause()
        alert("Vous avez déjoué l'attaque !")

      } else {
        alert("Mauvais mot de passe")
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

.main {
  margin-top: 100px;
}

.countdown {
  font-size: 8em;
  color: #d00;
}
</style>
