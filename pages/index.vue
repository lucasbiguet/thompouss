<template lang="pug">
  section(class="container")
    .main(v-if="gameWon == undefined")
      .countdown
        countdown(ref="countdown" :time="time" v-if="time" @countdownstart="counting = true" @countdownpause="counting = false" @countdownend="gameWon = false" @countdownprogress="countdownProgress")
          template(slot-scope="props") {{ props.minutes }}:{{ props.seconds }}

      form
        h2 Mot de passe
        input(v-model="password" :disabled="showError")
        button(@click.prevent="validatePassword" :disabled="showError") Valider

        .wrong-password(v-if="showError") Mauvais mot de passe

    .game-lost(v-if="gameWon == false")

    .game-won(v-if="gameWon == true")

    .debug
      label Mode administrateur:
      button(@click.prevent="$refs.countdown.pause" v-if="counting") Pause
      button(@click.prevent="$refs.countdown.start" v-else) Lancer
      button(@click.prevent="reset") Réinitialiser jeu
      button(@click.prevent="time = 27.1 * 60 * 1000") 27 min 5 s
      button(@click.prevent="time = 5 * 1000") 5 s
      button(@click.prevent="alarm.play()") Test alarme

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
      alarm: undefined,
      showError: false
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
        this.alarm.play()
      }
    }
  },

  mounted () {
    this.alarm = new Audio(this.alarmFile)
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

.container {
  height: 100%;
}

.main {
  margin-top: 30vh;
}

section {
  text-align: center;
  height: 100%;
}

.countdown {
  font-size: 12em;
  color: #d00;
}

.wrong-password {
  color: #d00;
}

.game-won {
  background: url('~/assets/escapgame-ecran-gagne.jpg') no-repeat center center fixed;
}

.game-lost {
  background: url('~/assets/escapgame-ecran-perdu.jpg') no-repeat center center fixed;
}

.game-won, .game-lost {
  width: 100%;
  height: 100vh;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover;
}
</style>
