<template lang="pug">
  .game-container
    .main(v-if="gameWon == undefined")
      img.logo(src="~/assets/logo.png")

      .columns.has-text-centered
        .column.is-12
          countdown(ref="countdown" :time="time" @countdownstart="counting = true" @countdownpause="counting = false" @countdownend="gameOver" @countdownprogress="countdownProgress")
            .countdown.has-text-danger(slot-scope="props") {{ props.minutes }}:{{ props.seconds }}

      .columns
        .column.is-4.is-offset-4
          form
            .field
              label.label.is-large.has-text-centered.has-text-danger Mot de passe
              .control
                input.input.is-medium.is-danger(v-model="password" :disabled="showError")
            .has-text-danger.has-text-centered(v-if="showError") Mauvais mot de passe
            .field(v-else)
              .control.has-text-centered
                button.button.is-medium.is-danger(@click.prevent="validatePassword" :disabled="showError") Valider

    .game-lost(v-if="gameWon == false")

    .game-won(v-if="gameWon == true")

    .debug(v-if="admin")
      h4.has-text-white Mode administrateur
      button.button.is-small(@click.prevent="$refs.countdown.pause" v-if="counting") Pause
      button.button.is-small(@click.prevent="$refs.countdown.start" v-else) Lancer
      button.button.is-small(@click.prevent="reset") Réinitialiser jeu
      button.button.is-small(@click.prevent="time = 27.1 * 60 * 1000") 27 min 5 s
      button.button.is-small(@click.prevent="time = 5 * 1000") 5 s
      button.button.is-small(@click.prevent="playAlarm") Test alarme
      button.button.is-small(@click.prevent="playGameOverSound") Test son fin
      button.button.is-small.is-success.is-pulled-right(@click.prevent="startGame") Fermer et commencer le jeu

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
      gameOverMusicFile: require('~/assets/game-over.mp3'),
      showError: false,
      admin: true
    }
  },

  methods: {
    validatePassword () {
      if (this.password == "missionhandicap") {
        this.$refs.countdown.pause()
        this.gameWon = true
      } else {
        this.showError = true
        setTimeout( () => { this.showError = false }, 3000)
      }
    },

    gameOver () {
      this.gameWon = false
      this.playGameOverSound()
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

    playGameOverSound () {
      let audio = new Audio(this.gameOverMusicFile)
      audio.play()
    },

    startGame () {
      this.reset()
      this.admin = false
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
  padding: 5px 10px;

  button, h4
    margin-right: 5px

  h4
    display: inline

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
