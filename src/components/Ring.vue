<template>
  <div class="ring">
    <canvas class="mycircle" width="518" height="518"></canvas>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator'

@Component
export default class Ring extends Vue {
  @Prop() total!: number
  @Prop() current!: number
  @Prop() startAt!: number

  mounted(): void {
    this.redraw()
  }

  @Watch('current')
  onCurrentChange(): void {
    this.redraw()
  }

  private redraw() {
    const canvas = this.$el.getElementsByClassName(
      'mycircle'
    )[0] as HTMLCanvasElement
    let context = canvas.getContext('2d')
    context?.clearRect(0, 0, canvas.width, canvas.height)
    const centerX = canvas.width / 2
    const centerY = canvas.height / 2
    const radius = 254

    if (context) {
      context.beginPath()
      const percent = this.current == 0 ? 1 : this.current / this.total
      const start = this.startPos * Math.PI
      const end = (this.startPos + 2) * Math.PI
      const position = (end - start) * percent + start
      context.arc(centerX, centerY, radius, start, position, false)
      context.lineWidth = 8
      context.strokeStyle = this.current == 0 ? '#900a0a' : '#09a65a'
      context.stroke()
    }
  }

  get startPos(): number {
    return this.startAt || 1.5
  }
}
</script>

<style></style>
