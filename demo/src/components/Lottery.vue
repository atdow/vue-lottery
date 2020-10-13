<template>
  <div class="lottery">
     
    <div class="luckDraw">
      <ul class="nineGrid">
        <li class="row">
          <div
            v-for="(n, key) in 3"
            :key="n"
            :class="index === key ? `active` : ``"
          >
            <div class="wrapper">
              <img src="../assets/luck-icon.png" alt="" />
              <p>8金转</p>
            </div>
            <div class="mask"></div>
          </div>
        </li>
        <li class="row">
          <div :class="index === 7 ? 'active' : ''">
            <div class="wrapper">
              <img src="../assets/luck-icon.png" alt="" />
              <p>128金转</p>
            </div>
            <div class="mask"></div>
          </div>
          <div class="getLuck" @click="startLottery">
            <p>立即<br />抽奖</p>
          </div>
          <div :class="index === 3 ? 'active' : ''">
            <div class="wrapper">
              <img src="../assets/luck-icon.png" alt="" />
              <p>128金转</p>
            </div>
            <div class="mask"></div>
          </div>
        </li>
        <li class="row">
          <div
            v-for="(n, key) in 3"
            :key="n"
            :class="index === 6 - key ? `active` : ``"
          >
            <div class="wrapper">
              <img src="../assets/luck-icon.png" alt="" />
              <p>256金转</p>
            </div>
            <div class="mask"></div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
 
<script>
export default {
  name: "lottery",
  props: {},
  data() {
    return {
      title: "积分转盘",
      index: -1, // 当前转动到哪个位置，起点位置
      count: 8, // 总共有多少个位置
      timer: 0, // 每次转动定时器
      speed: 200, // 初始转动速度
      times: 0, // 转动次数
      cycle: 50, // 转动基本次数：即至少需要转动多少次再进入抽奖环节
      prize: -1, // 中奖位置
      click: true,
      showToast: false,
      toastType: "luck",
    };
  },
  components: {},
  watch: {},
  computed: {},
  created() {},
  mounted() {},
  beforeDestroy() {},
  methods: {
    // 开始抽奖
    startLottery() {
      if (!this.click) {
        return;
      }
      this.closeToast();
      this.speed = 200;
      this.click = false;
      this.startRoll();
    },

    // 开始转动
    startRoll() {
      this.times += 1; // 转动次数
      this.oneRoll(); // 转动过程调用的每一次转动方法，这里是第一次调用初始化

      // 如果当前转动次数达到要求 && 目前转到的位置是中奖位置
      if (this.times > this.cycle + 10 && this.prize === this.index) {
        clearTimeout(this.timer); // 清除转动定时器，停止转动
        this.prize = -1;
        this.times = 0;
        this.click = true;
        this.showToast = true;
        this.toastType = "comeOn";
        console.log("你已经中奖了");
      } else {
        if (this.times < this.cycle) {
          this.speed -= 10; // 加快转动速度
        } else if (this.times === this.cycle) {
          // 随机获得一个中奖位置
          const index = parseInt(Math.random() * 10, 0) || 0;
          this.prize = index;
          if (this.prize > 7) {
            this.prize = 7;
          }
          console.log(`中奖位置${this.prize}`);
        } else if (
          this.times > this.cycle + 10 &&
          ((this.prize === 0 && this.index === 7) ||
            this.prize === this.index + 1)
        ) {
          this.speed += 110;
        } else {
          this.speed += 20;
        }

        if (this.speed < 40) {
          this.speed = 40;
        }
        this.timer = setTimeout(this.startRoll, this.speed);
      }
    },

    // 每一次转动
    oneRoll() {
      let index = this.index; // 当前转动到哪个位置
      const count = this.count; // 总共有多少个位置
      index += 1;
      if (index > count - 1) {
        index = 0;
      }
      this.index = index;
    },

    // 关闭弹出框
    closeToast() {
      this.showToast = false;
    },
  },
};
</script>
 
<style lang="scss" scoped>
.luckDraw {
  .nineGrid {
    position: absolute;

    li {
      height: 200px;
      display: flex;
      > div {
        flex: 1;
        margin-right: px3rem(5);
        width: 200px;
        height: 100%;
        text-align: center;
        position: relative;
        background: red;

        .wrapper {
          width: 100%;
          height: 100%;
          margin: 0;
          //   @include background-cover("background-grid.png");
          img {
            width: 100px;
            height: 100px;
            vertical-align: middle;
            margin-top: px3rem(8);
          }
        }

        .mask {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          opacity: 0.5;
          border-radius: px3rem(10);
          background-color: #000;
          display: none;
        }
      }
      // 遮罩层的出现
      > div.active {
        .mask {
          display: block;
        }
      }

      // 抽奖按钮
      > div.getLuck {
        // @include background-cover("background-getLuck.png");
        background: yellow;
        p {
          color: red;
        }
      }
    }

    li:last-child {
      margin-bottom: px3rem(5);
    }
  }
}
</style>