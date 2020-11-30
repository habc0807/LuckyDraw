<template>
  <section class="lucky-draw">
    <div
      v-for="(item, index) in prizes"
      :key="item.giftId"
      class="prize-item"
      :class="[item.isChose  ? 'speed-chose' : '', stopIndex === index ? 'stop' : '']"
    >
      <img class="prize-item-thumb" :src="item.giftPic" :alt="index + 1" />
      <span class="prize-index">{{index}}</span>
    </div>
  </section>
</template>

<script>
export default {
  name: 'Game',
  props: {
    prizesList: {
      type: Array,
      default: [],
      required: true
    },
    resultIndex: {
      type: Number,
      default: -1,
      required: true
    },
  },
  data() {
    return {
      prizes: this.prizesList, // data奖品数组
      arrNum: [0, 1, 2, 3, 6, 5, 4], // 转动顺序
      isRolling:  false, // 是否正在抽奖
      prizeIndex: 0, // 转动指针
      timer1: null, // 定时器1 快速旋转
      timer2: null, // 定时器2 慢速旋转
      stopIndex: -1, // 指针最终的位置 是arrNum[resultIndex] 转动终止的映射
      fastSpeed:  100, // 抽奖动画，经过每个商品的时间
      slowSpeed: 200, // 抽奖动画，经过每个商品的时间
    }
  },
  created() {
    console.log(this.prizesList)
    console.log(this.resultIndex)
  },
  watch: {
    resultIndex: {
      handler(v){
        // console.log('resultIndex: ', resultIndex);
        if (v !== -1) {
          if (this.arrNum.indexOf(v) !== -1) {
            this.stopIndex = this.arrNum.indexOf(v);
            this.drawPrize();
          }
        } else {
          this.clearPrize();
        }
      },
      immediate: true
    },
  },
  methods: {
    // 抽奖重置
    clearPrize() {
      this.prizes = this.prizes.map((item) => {
        item.isChose = false;
        return item;
      });

      this.isRolling = false;
      this.stopIndex = -1;
    },

    // 点击抽奖 执行快速转动，2秒后执行慢速转动
    drawPrize() {
      if (!this.isRolling && this.stopIndex !== -1) {
        this.isRolling = true;
        this.timer1 = setInterval(this.fastMove, this.fastSpeed);

        //转三圈之后降速
        this.timer2 = setTimeout(() => {
          clearInterval(this.timer1);
          this.lowSpeed();
        }, this.prizes.length * this.fastSpeed * 3);
      }
    },

    // 抽奖移动函数 纯移动 不终止 直到定时器清除
    fastMove() {
      const { prizes, arrNum, prizeIndex } = this;
      const LEN = prizes.length;

      prizes[arrNum[prizeIndex]].isChose = 1;
      prizes[arrNum[prizeIndex - 1 < 0 ? LEN - 1 : prizeIndex - 1]].isChose = 0;
      this.prizeIndex = prizeIndex + 1 === LEN ? 0 : prizeIndex + 1;
    },

    // 抽奖移动函数 如果指针位置prizeIndex与结果位置相同，则抽奖完成，清除所有定时器
    slowMove() {
      const { prizes, arrNum, prizeIndex, resultIndex, stopIndex } = this;
      const LEN = prizes.length;

      prizes[arrNum[prizeIndex]].isChose = 2;
      prizes[arrNum[prizeIndex - 1 < 0 ? LEN - 1 : prizeIndex - 1]].isChose = 0;

      if (stopIndex !== -1 && stopIndex === prizeIndex) {
        clearInterval(this.timer1);
        clearInterval(this.timer2);

        this.$emit('canShowResultPop');
      } else {
        this.prizeIndex = prizeIndex + 1 === LEN ? 0 : prizeIndex + 1;
      }
    },

    // 抽奖慢速转动 得到抽奖结果，定位好结果奖品的位置，速度降低
    lowSpeed() {
      const { arrNum, resultIndex } = this;
      this.timer2 = setInterval(this.slowMove, this.slowSpeed);
    },

    destroyed() {
      clearInterval(this.timer1);
      clearInterval(this.timer2);
    },
  }
}
</script>
<style lang="scss" scoped>
.lucky-draw {
  padding-top: 50px;
  display: flex;

  justify-content: center;
  flex-wrap: wrap;
  align-content: flex-start;
  .prize-item {
    position: relative;

    display: flex;

    width: 50px;
    max-width: 50px;
    height: 50px;
    max-height: 50px;
    margin: 0 16px 22px 16px;

    background: url("../assets/icons/prize.min.png") no-repeat;
    background-size: 50px 50px;

    align-items: center;
    justify-content: center;

    .prize-item-thumb {
      width: 35px;
      height: 35px;

      transition: 0.3s;
      transform: scale(1);
    }
    .prize-index {
      color: #fc696a;
      font-size: 12px;
    }
    &::before {
      position: absolute;
      z-index: 3;
      top: -5px;
      left: -5px;

      width: 60px;
      height: 60px;

      content: "";
      transition: 0.5s;

      opacity: 0;
      background: rgba(255, 255, 255, 0.5);
      background: url("../assets/icons/120-120.min.png") no-repeat;
      background-size: 60px 60px;
    }
  }
  .speed-chose,
  .slow-chose {
    position: relative;
    &::before {
      transition: 0.1s;

      opacity: 1;
    }
    .prize-item-thumb {
      transform: scale(1.3);
    }
  }
}
</style>

