<template>
  <div id="app">
    <Timer :minutes="0" :seconds="15" @endCountdown="endCountdown" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import Timer from './components/Timer.vue'

@Component({
  components: {
    Timer
  }
})
export default class App extends Vue {
  endCountdown(): void {
    alert("time's up")
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@700&display=swap');

@font-face {
  font-family: 'bebas';
  src: url('./assets/fonts/bebasneue-book-webfont.woff') format('woff'),
    url('./assets/fonts/bebasneue-book-webfont.woff2') format('woff2');
}

body {
  align-items: center;
  background: #2b2a30;
  display: flex;
  justify-content: center;
  margin: 0;
  min-height: 100vh;
  min-width: 100vw;
  padding: 0;
}
</style>
