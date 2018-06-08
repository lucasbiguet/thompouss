<template lang="pug">
  .game-container
    .main(v-if="gameWon == undefined")
      img.logo(src="~/assets/logo.png")

      .columns.has-text-centered
        .column.is-12
          countdown(ref="countdown" :time="time" v-if="time" @countdownstart="counting = true" @countdownpause="counting = false" @countdownend="gameWon = false" @countdownprogress="countdownProgress")
            .countdown.has-text-danger(slot-scope="props") {{ props.minutes }}:{{ props.seconds }}

      .columns
        .column.is-4.is-offset-4
          form
            .field
              label.label.is-large.has-text-centered Mot de passe
              .control
                input.input.is-medium(v-model="password" :disabled="showError")
            .has-text-danger.has-text-centered(v-if="showError") Mauvais mot de passe
            .field(v-else)
              .control.has-text-centered
                button.button.is-medium.is-danger(@click.prevent="validatePassword" :disabled="showError") Valider

    .game-lost(v-if="gameWon == false")

    .game-won(v-if="gameWon == true")

    .debug
      label Mode administrateur:
      button(@click.prevent="$refs.countdown.pause" v-if="counting") Pause
      button(@click.prevent="$refs.countdown.start" v-else) Lancer
      button(@click.prevent="reset") Réinitialiser jeu
      button(@click.prevent="time = 27.1 * 60 * 1000") 27 min 5 s
      button(@click.prevent="time = 5 * 1000") 5 s
      button(@click.prevent="playAlarm") Test alarme
      button(@click.prevent="playWinSound") Test son fin

</template>

<script>
import Countdown from '@xkeshi/vue-countdown'

export default {
  components: {
    Countdown
  },

  data () {
    return {
      time: 30 * 60 * 1000,
      password: "",
      gameWon: undefined,
      counting: false,
      alarmFile: require('~/assets/alarm.ogg'),
      winMusicFile: require('~/assets/win.mp3'),
      showError: false
    }
  },

  methods: {
    validatePassword () {
      if (this.password == "missionhandicap") {
        this.$refs.countdown.pause()
        this.gameWon = true
        this.playWinSound()
      } else {
        this.showError = true
        setTimeout( () => { this.showError = false }, 3000)
      }
    },

    reset () {
      this.time = undefined
      this.$nextTick(() => {
        this.time = 30 * 60 * 1000
      })
      this.gameWon = undefined
      this.password = ""
    },

    countdownProgress (count) {
      if (count.minutes == 27 && count.seconds == 0) {
        this.playAlarm()
      }
    },

    playAlarm () {
      let audio = new Audio(this.alarmFile)
      audio.play()
    },

    playWinSound () {
      let audio = new Audio(this.winMusicFile)
      audio.play()
    }
  }
}
</script>

<style lang="sass" scoped>
.has-text-danger
  color: #d00 !important

.debug
  position: fixed
  width: 100%
  bottom: 0
  background-color: grey

.game-container
  height: 100%

.main
  background: url('~/assets/escapgame-ecran-univers.jpg') no-repeat center center fixed

  .logo
    margin: 10px

section
  text-align: center
  height: 100%

.countdown
  font-size: 12em
  font-weight: 500
  //color: #d00

.wrong-password
  //color: #d00

.game-won
  background: url('~/assets/escapgame-ecran-gagne.jpg') no-repeat center center fixed

.game-lost
  background: url('~/assets/escapgame-ecran-perdu.jpg') no-repeat center center fixed

.main, .game-won, .game-lost
  width: 100%
  height: 100vh
  -webkit-background-size: cover
  -moz-background-size: cover
  -o-background-size: cover
  background-size: cover
</style>
