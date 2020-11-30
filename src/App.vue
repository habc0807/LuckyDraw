<template>
  <div>
    <img alt="Vue logo" src="./assets/logo.min.png" />
    <Game
        :prizesList="prizes"
        :resultIndex.sync="resultIndex"
        :test="test"
        @canShowResultPop="canShowResultPopHandle"
    />
  </div>
</template>

<script>
import Game from './components/Game.vue'
import { prizes } from './config.js'

export default {
  name: 'App',
  components: {
    Game
  },
  data() {
    return {
      prizes: prizes,
      resultIndex: 3,
      test: 1,
    }
  },
  // created() {
  //   setTimeout(() => {
  //     this.prizes = prizes;
  //     console.log(this.prizes)
  //   }, 100)
  // },
  methods: {
    canShowResultPopHandle() {
      // console.log('抽奖成功')
    }
  }
}
</script>
