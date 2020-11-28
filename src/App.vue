<template>
  <div>
    <img alt="Vue logo" src="./assets/logo.png" />
    <Game
        :prizesList="prizes"
        :resultIndex.sync="resultIndex"
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
      resultIndex: 3
    }
  },
  methods: {
    canShowResultPopHandle() {
      // console.log('抽奖成功')
    }
  }
}
</script>
