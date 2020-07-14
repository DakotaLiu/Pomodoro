<template>
  <div id="home">
    <vue-ellipse-progress
      :is="component"
      id="timer-example"
      :progress="progress"
      :determinate="determinate"
      color="#FFC408"
      empty-color="#FAD689"
      :emptyColorFill="emptyColorFill"
      thickness="3%"
      emptyThickness="4%"
      :size="300"
      dash="strict 60 0.8"
      lineMode="in-over"
      :legend="false"
      legendClass="legend-custom-style"
      fontSize="2.5rem"
      font-color="#AB3B3A"
      animation="loop 1000 100"
      :loading="loading"
      :no-data="noData"
    >
      <span slot="legend-caption">
        <h4>{{ currentText }}</h4>
        <h1>{{ timetext }}</h1>
      </span>
    </vue-ellipse-progress>
    <div class="buttons">
    <b-btn variant="warning" v-if="status!=1" @click="start">
      <font-awesome-icon :icon="['fas','play']"></font-awesome-icon>
    </b-btn>
    <b-btn variant="danger" v-if="status==1" @click="pause">
      <font-awesome-icon :icon="['fas','pause']"></font-awesome-icon>
    </b-btn>
    <b-btn variant="primary" v-if="current.length > 0 || todos.length > 0" @click="finish(true)">
      <font-awesome-icon :icon="['fas','step-forward']"></font-awesome-icon>
    </b-btn>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      // 0 = 停止
      // 1 = 播放
      // 2 = 暫停
      status: 0,
      timer: 0,
      // sec: 0,
      // min: 0,
      emptyColor: {
        radial: false,
        colors: [
          {
            color: '#AB3B3A',
            offset: '0',
            opacity: '1'
          },
          {
            color: '#F19483',
            offset: '100',
            opacity: '1'
          }
        ]
      },
      emptyColorFill: {
        radial: true,
        colors: [
          {
            color: '#FFC408',
            offset: '50',
            opacity: '0.2'
          },
          {
            color: '#FFC408',
            offset: '50',
            opacity: '0.15'
          },
          {
            color: '#FFC408',
            offset: '70',
            opacity: '0.15'
          },
          {
            color: '#FFC408',
            offset: '70',
            opacity: '0.1'
          },
          {
            color: '#FFC408',
            offset: '90',
            opacity: '0.1'
          },
          {
            color: 'transparent',
            offset: '90',
            opacity: '0.1'
          },
          {
            color: 'transparent',
            offset: '95',
            opacity: '0.1'
          },
          {
            color: 'transparent',
            offset: '95',
            opacity: '0.1'
          }
        ]
      }
    }
  },
  computed: {
    progress () {
      const m = Math.floor(this.timeleft / 60)
      const s = Math.floor(this.timeleft % 60)
      const total = this.timeleft - (60 * `${m}` + `${s}`)
      return `${total}` / 1500
    },
    component () {
      return this.test ? 'vue-ellipse-progress-test' : 'vue-ellipse-progress'
    },
    currentText () {
      return this.current.length > 0
        ? this.current
        : this.todos.length > 0
          ? '點擊開始'
          : '沒有事項'
    },
    timetext () {
      const m = Math.floor(this.timeleft / 60)
      const s = Math.floor(this.timeleft % 60)
      return `${m} : ${s}`
    },
    alarm () {
      return this.$store.getters.alarm
    },
    timeleft () {
      return this.$store.getters.timeleft
    },
    current () {
      return this.$store.getters.current
    },
    todos () {
      return this.$store.getters.todos
    }
  },
  methods: {
    start () {
      if (this.status === 2) {
        // 暫停後繼續
        this.status = 1
        this.timer = setInterval(() => {
          this.$store.commit('countDown')
          if (this.timeleft <= 0) {
            this.finish(false)
          }
        }, 1000)
      } else {
        // 開始全新的倒數
        if (this.todos.length > 0) {
          // 向store傳達開始全新倒數
          this.$store.commit('start')
          this.status = 1
          this.timer = setInterval(() => {
            this.$store.commit('countDown')
            if (this.timeleft <= 0) {
              this.finish(false)
            }
          }, 1000)
        }
      }
    },
    finish (skip) {
      clearInterval(this.timer)
      this.status = 0
      this.$store.commit('finish')
      if (!skip) {
        const audio = new Audio()
        audio.src = './alarms/' + this.alarm
        audio.play()
      }
      if (this.todos.length > 0) {
        this.start()
      } else {
        alert('結束')
      }
    },
    pause () {
      clearInterval(this.timer)
      this.status = 2
    }
  }
}

</script>
