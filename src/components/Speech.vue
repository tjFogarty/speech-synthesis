<template>
  <div>

    <transition name="fade" v-if="isLoading">
      <pulse-loader></pulse-loader>
    </transition>

    <transition name="fade" v-if="!isLoading">
      <div class="quiz-container">

        <form @submit.prevent="greet">
          <h1>Speech Example</h1>

          <div class="form-group" v-if="voiceList.length">
            <label for="voices">Select a voice</label>
            <select class="form-control" id="voices" v-model="selectedVoice">
              <option v-for="(voice, index) in voiceList" :data-lang="voice.lang" :value="index">
                {{ voice.name }} ({{ voice.lang }})
              </option>
            </select>
          </div>

          <div class="form-group">
            <label for="your-name">Your name</label>
            <input class="form-control" id="your-name" type="text" v-model="name" required>
          </div>

          <button type="submit" class="btn btn-success">Greet</button>
        </form>

      </div>
    </transition>

  </div>
</template>

<script>
import PulseLoader from 'vue-spinner/src/PulseLoader.vue'

export default {
  name: 'speech',

  data () {
    return {
      isLoading: true,
      name: '',
      selectedVoice: 0,
      synth: window.speechSynthesis,
      voiceList: [],
      greetingSpeech: new window.SpeechSynthesisUtterance()
    }
  },

  components: {
    PulseLoader
  },

  mounted () {
    // wait for voices to load
    window.speechSynthesis.onvoiceschanged = () => {
      this.voiceList = this.synth.getVoices()

      // give a bit of delay to show loading screen
      // just for the sake of it, I suppose. Not the best reason
      setTimeout(() => {
        this.isLoading = false
      }, 800)
    }

    this.listenForSpeechEvents()
  },

  methods: {
    /**
     * React to speech events
     */
    listenForSpeechEvents () {
      this.greetingSpeech.onstart = () => {
        this.isLoading = true
      }

      this.greetingSpeech.onend = () => {
        this.isLoading = false
      }
    },

    /**
     * Shout at the user
     */
    greet () {
      // it should be 'craic', but it doesn't sound right
      this.greetingSpeech.text = `Hello, ${this.name}. What's the crack?`

      this.greetingSpeech.voice = this.voiceList[this.selectedVoice]

      this.synth.speak(this.greetingSpeech)
    }
  }
}
</script>

<style scoped>
  .quiz-container {
    min-width: 100vw;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  form {
    padding: 30px;
    max-width: 600px;
    margin: 0 auto;
    background: #fff;
    border-radius: 3px;
    box-shadow: 0 10px 30px 10px rgba(0, 0, 0, 0.1);
  }

  .v-spinner {
    position: fixed;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(255, 255, 255, 0.8);
    -webkit-backdrop-filter: blur(4px);
    backdrop-filter: blur(4px);
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity ease .5s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }

  .btn-success {
    background: #43C6AC;
    border-color: #43C6AC;
    cursor: pointer;
  }

  h1 {
    margin-bottom: 25px;
  }
</style>
