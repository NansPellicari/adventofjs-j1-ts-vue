<template>
  <div class="wrapper">
    <Ring :total="totalSeconds" :current="currentSeconds" />

    <div class="timer">
      <div class="time">
        <div class="minutes">
          <input
            type="text"
            maxlength="2"
            :value="remainMinutesFormatted"
            :disabled="settingsAreDisable"
            @change="changeTime($event, ETimeSection.minutes)"
            @keyup.enter="saveSettings"
          />
        </div>
        <div class="colon">:</div>
        <div class="seconds">
          <input
            type="text"
            maxlength="2"
            :value="remainSecondsFormatted"
            :disabled="settingsAreDisable"
            @change="changeTime($event, ETimeSection.seconds)"
            @keyup.enter="saveSettings"
          />
        </div>
      </div>
      <button class="start" @click="stop" v-if="shouldStop">stop</button>
      <button class="start" @click="pause" v-else-if="shouldPause">
        pause
      </button>
      <button class="start" @click="start" v-else>start</button>
      <button
        class="settings"
        :class="{ edit: !settingsAreDisable }"
        @click="changeSettings"
        :disabled="played"
      >
        <img src="../assets/images/gear.svg" alt="Settings" />
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import Ring from './Ring.vue'
import { Component, Prop, Watch, Vue } from 'vue-property-decorator'

enum ETimeSection {
  minutes = 'MIN',
  seconds = 'SEC'
}

@Component({
  components: {
    Ring
  }
})
export default class Timer extends Vue {
  @Prop() minutes!: number
  @Prop() seconds!: number

  played = false
  canEditSettings = false
  ETimeSection = ETimeSection
  private startMinutes = 0
  private startSeconds = 0
  private remainMinutes = 0
  private remainSeconds = 0
  private timerId = 0

  mounted(): void {
    this.resetMinutes(this.minutes)
    this.resetSeconds(this.seconds)
  }

  start(): void {
    this.timerId = setInterval(() => {
      this.currentSeconds--
    }, 1000)
    this.played = true
  }

  pause(): void {
    clearInterval(this.timerId)
    this.played = false
    this.timerId = 0
  }

  stop(): void {
    this.pause()
    this.remainSeconds = this.startSeconds
    this.remainMinutes = this.startMinutes
  }

  @Watch('currentSeconds', { immediate: false })
  onPropertyChanged(value: number): void {
    if (value == 0) {
      clearInterval(this.timerId)
      setTimeout(() => {
        this.$emit('endCountdown')
      }, 100)
    }
  }

  changeTime(e: Event, type: ETimeSection): void {
    const value = (e.target as HTMLInputElement).value
    if (type === ETimeSection.minutes) {
      this.resetMinutes(parseInt(value))
    } else {
      this.resetSeconds(parseInt(value))
    }
  }

  private resetMinutes(value: number) {
    this.startMinutes = value
    this.remainMinutes = this.startMinutes
  }
  private resetSeconds(value: number) {
    this.startSeconds = value
    this.remainSeconds = this.startSeconds
  }

  saveSettings(): void {
    this.canEditSettings = false
  }
  changeSettings(): void {
    this.canEditSettings = !this.canEditSettings
  }

  unmounted(): void {
    if (this.timerId != 0) {
      this.stop()
    }
  }

  static toSeconds(minutes: number, seconds: number): number {
    return minutes * 60 + seconds
  }

  get shouldPause(): boolean {
    return this.played && this.totalSeconds > 0
  }

  get shouldStop(): boolean {
    return this.currentSeconds == 0
  }

  get settingsAreDisable(): boolean {
    return !this.canEditSettings || this.played
  }

  get totalSeconds(): number {
    return Timer.toSeconds(this.startMinutes, this.startSeconds)
  }
  get currentSeconds(): number {
    return Timer.toSeconds(this.remainMinutes, this.remainSeconds)
  }
  set currentSeconds(value: number) {
    this.remainMinutes = Math.floor(value / 60)
    this.remainSeconds = Math.floor(value % 60)
  }

  get remainMinutesFormatted(): string {
    return this.remainMinutes.toLocaleString('en-US', {
      minimumIntegerDigits: 2,
      useGrouping: false
    })
  }

  get remainSecondsFormatted(): string {
    return this.remainSeconds.toLocaleString('en-US', {
      minimumIntegerDigits: 2,
      useGrouping: false
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* outer glow */
.wrapper {
  align-items: center;
  border-radius: 50%;
  box-shadow: -5px 14px 44px #000000, 5px -16px 50px rgba(255, 255, 255, 0.15);
  display: flex;
  height: 518px;
  justify-content: center;
  position: relative;
  width: 518px;
}

.ring {
  position: absolute;
  left: 0;
  top: 0;
  z-index: 1;
}

/* inner circle */
.timer {
  align-items: center;
  background: radial-gradient(
    71.4% 71.4% at 51.7% 28.6%,
    #3a393f 0%,
    #17171a 100%
  );
  border-radius: 50%;
  box-shadow: inset 0px 0px 114px rgba(0, 0, 0, 0.45);
  color: white;
  display: flex;
  flex-direction: column;
  height: 500px;
  justify-content: center;
  position: relative;
  width: 500px;
  z-index: 2;
}

/* actual time */
.time {
  display: flex;
  font-family: 'bebas';
  font-size: 196px;
  margin: 30px auto;
  position: relative;
  top: 30px;
}

input[type='text'] {
  border: 0;
  border-bottom: 1px dashed white;
  background: none;
  color: white;
  font-family: 'bebas';
  font-size: 196px;
  height: 170px;
  width: 150px;
  text-align: center;
  outline: none;
}

input[type='text']:disabled {
  border-bottom: none;
}

.start {
  cursor: pointer;
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  letter-spacing: 10px;
  line-height: 20px;
  background: none;
  color: white;
  opacity: 0.5;
  border: none;
  text-transform: uppercase;
  margin-bottom: 20px;
}

.start:hover {
  opacity: 1;
}

.settings {
  border: none;
  background: none;
  cursor: pointer;
  opacity: 0.3;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.settings img {
  padding-top: 8px;
}

.settings.edit {
  background: rgba(255, 255, 255, 0.2);
  opacity: 1;
}

.settings:hover {
  opacity: 1;
}
</style>
