<template>
  <div id="app">
    <div class="f7-wrapper">
      <div class="f7-page f7_intro" :class="introStyle" v-show="introVisible">
        <div class="wbd_card">
        	<div class="intro_hd">
        		<h1 class="intro_title">
        			飞雪连天射白鹿 <br>
              笑书神侠倚碧鸳
        		</h1>
        		<p class="intro_desc">
              金大侠江湖急急如律令
        			<br>
              60秒带你笑傲江湖
        		</p>
        	</div>
        	<div class="intro_bd">
        		<p class="intro_slogan">
        		</p>
        		<a ontouchstart="" href="javascript:" class="intro_start wbd_btn" @click="start">
        			快意恩仇
        		</a>
        	</div>
        </div>
      </div>
      <div class="questions-box">
        <div class="f7-page" v-for="(item, index) in pages" :class="item.styObj" :style="{ zIndex: 99 - index}">
          <div class="wbd_card">
            <div class="question_hd">
              <span class="question_index">Q{{ ++index }}</span>
              <p class="question_title">{{item.title}}</p>
            </div>
            <div class="question_bd">
              <a class="question_answer" :class="{ success: item.reply ? (subItem.type === item.success ? true : false) : false }" ontouchstart="" href="javascript:" @click="answerQuestion(subItem.type, item.success, item.ID, item.reply)" v-for="(subItem, index) in item.questions" data-idx="0">
                <span class="question_answer_bg"></span>
                <span class="question_answer_text">{{subItem.type}}    {{subItem.text}}</span>
              </a>
            </div>
            <p v-if="item.reply">点击正确答案查看下一题</p>
            <span class="question_ft"> {{ index }} / {{ pagesLen }}</span>
          </div>
        </div>
      </div>
      <div class="questions-result">
        <div class="f7-page f7_result" v-show="resultVisible" :class="resultStyle">
          <div class="wbd_card">
          	<div class="intro_hd">
          		<h1 class="intro_title">
          			测试时间{{60 - spendTime}}S
          		</h1>
          		<p class="intro_desc">
                测试分数：{{cacheCorrect.length}}分
          		</p>
          	</div>
          	<div class="intro_bd">
          		<p class="intro_slogan">
                <a ontouchstart="" href="">
            			重新测试
            		</a>
          		</p>
          		<a ontouchstart="" @click="checkReply" class="intro_start wbd_btn">
          			查看答案
          		</a>
          	</div>
          </div>
        </div>
      </div>
    </div>
    <div class="count-down">
      <canvas id="canvasId" width="256" height="256" v-show="timerVisible"></canvas>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      introStyle: '',
      introVisible: true,
      resultVisible: true,
      timerVisible: true,
      resultStyle: '',
      pages: [],
      cachePages: null,
      cache: [],
      pagesLen: 0,
      cacheCorrect: [],
      timer: null,
      spendTime: 0, // 花费的时间
      lastId: null // 第一道题的id
    }
  },
  mounted () {
    this.fetchData()
  },
  methods: {
    fetchData () {
      let self = this
      this.$http.get('./static/data.json')
      .then(function (response) {
        let result = response.data
        if (result.error === 0) {
          result.data.map(function (item) {
            item.styObj = ''
            item.reply = false
          })
          self.$set(self, 'pages', result.data)
          self.$set(self, 'cachePages', result.data)
          self.pagesLen = result.data.length
          self.lastId = result.data[self.pagesLen - 1].ID
        }
      })
      .catch(function (error) {
        console.log(error)
      })
    },
    start () {
      this.introStyle = 'magicRotate'
      this.startCountDown()
    },
    answerQuestion (currentId, realId, id, reply) {
      let selectData = this.pages.find(function (item) {
        return item.ID === id
      })
      selectData.styObj = 'magicRotate'
      this.$set(self, 'pages', this.pages)
      if (reply) {
        return false
      }
      if (currentId === realId) {
        // 缓存答案
        this.cacheCorrect.push('true')
      }
      // 最后一个元素，选完打卡下班
      if (id === this.lastId) {
        this.resultStyle = 'magic'
        // 清除计时器
        clearInterval(this.timer)
        this.timerVisible = false
      }
      // let remove_element = this.pages.shift()
      // this.pages.push(remove_element)
    },
    // 开启倒计时
    startCountDown () {
      let self = this
      let canvas = document.getElementById('canvasId')
      let context = canvas.getContext('2d') // 获取画图环境，指明为2d
      let centerX = canvas.width / 2 // Canvas中心点x轴坐标
      let centerY = canvas.height / 2 // Canvas中心点y轴坐标
      // let radius = canvas.width / 2
      let countDownSeconds = 60 // 计时器秒杀 (可修改)
      let rad = Math.PI * 2 / countDownSeconds  // 将360度分成countDownSeconds份，那么每一份就是rad度
      let currentSeconds = 60  // 加载的快慢就靠它了
      // 绘制蓝色外圈
      function blueCircle(currentSeconds){
        let progress = 360 * currentSeconds / countDownSeconds
        let progress_pi = Math.PI * (progress / 180 - 1 / 2)
        context.save()
        context.strokeStyle = "#28A4FC" // 设置描边样式
        context.lineWidth = 4 // 设置线宽
        context.beginPath() // 路径开始
        context.arc(centerX, centerY, 70 , -Math.PI / 2, progress_pi, false) // 用于绘制圆弧context.arc(x坐标，y坐标，半径，起始角度，终止角度，顺时针/逆时针)
        context.lineCap = 'round'
        context.stroke() // 绘制
        context.closePath() // 路径结束
        context.restore()
      }
      // 绘制白色外圈
      function whiteCircle(){
        context.save()
        context.beginPath()
        context.strokeStyle = "white"
        context.arc(centerX, centerY, 70 , 0, Math.PI * 2, true)
        context.stroke()
        context.closePath()
        context.restore()
      }
      // 内部文字绘制
      function text(n){
        context.save() // save和restore可以保证样式属性只运用于该段canvas元素
        context.strokeStyle = "#fff" // 设置描边样式
        context.font = "40px Arial" // 设置字体大小和字体
        context.textAlign = "center"
        context.textBaseline = 'middle'
        //绘制字体，并且指定位置
        context.fillText(n + "S", centerX, centerY)
        // 抗锯齿
        context.globalCompositeOperation = 'source-atop'
        context.stroke() //执行绘制
        context.restore()
      }
      //动画循环
      self.timer = setInterval(function () {
        self.spendTime = currentSeconds
        if(currentSeconds <= 0) {
          currentSeconds = 0
          clearInterval(self.timer)
          self.timerVisible = false
          self.pages.map(function (item) {
            item.styObj = 'magicRotate'
          })
          self.$set(self, 'pages', self.pages)
          self.resultStyle = 'magic'
        }
        context.clearRect(0, 0, canvas.width, canvas.height)
        whiteCircle()
        text(currentSeconds)
        blueCircle(currentSeconds)
        currentSeconds--
      }, 1000)
    },
    // 查看答案
    checkReply () {
      this.pages.map(function (item) {
        item.styObj = ''
        item.reply = true
      })
      this.$set(this, 'pages', this.pages)
    }
  }
}
</script>

<style lang="less">
@import "./assets/less/index.less";
#app {
  min-width: 320px;
  max-width: 640px;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  padding-bottom: 0;
  font-size: 14px;
  overflow: hidden;
  margin: 0 auto;
}
.f7-wrapper {
  .f7-page {
    position: absolute;
    width: 100%;
    height: 100%;
    padding-bottom: 50%;
    // 卡片样式
    .wbd_card {
      position: relative;
      margin: 24px 36px 0;
      background: #fff;
      border-radius: 16px;
      box-shadow: 0 2px 32px 0 rgba(0,0,0,.1);
      // visibility: hidden;
      text-align: center;
      .intro_hd {
          padding: 0 24px 20px;
          background-image: linear-gradient(to bottom,#fff 0,#fcfcfc 100%);
          background-repeat: repeat-x;
          filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#FFFFFFFF', endColorstr='#FFFCFCFC', GradientType=0)
      }
      .intro_title {
        padding-top: 144px;
        // background: url(../../images/independent/logo/weread_logo_80h$b2111890.png) 50% 50% no-repeat;
        background-size: 80px 80px;
        color: #5d646e;
        font-size: 15px;
        line-height: 18px
      }
      .intro_bd{
        padding: 20px 24px 28px;
      }
      .intro_slogan {

        font-size: 13px;
        color: #adb4be;
        line-height: 21px;
        margin-bottom: 20px;
      }
      .wbd_btn {
        position: relative;
        display: block;
        height: 42px;
        line-height: 42px;
        color: #fff;
        background-image: linear-gradient(to bottom,#27ACFB 0,#2D9FFD 100%);
        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#FF27ACFB', endColorstr='#FF2D9FFD', GradientType=0);
        box-shadow: 0 8px 24px 0 rgba(26,136,238,.3);
        border-radius: 21px;
        font-size: 13px;
        font-weight: 500;
        letter-spacing: 1px;
        transition: all .2s
      }
      .question_hd{
        position: relative;
        height: 92px;
        padding: 4px 24px 0 36px;
        margin-bottom: 8px;
        background-image: linear-gradient(to right,#28A4FC 0,#2D9FFD 100%);
        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#FF28A4FC', endColorstr='#FF2D9FFD', GradientType=1);
      }
      .question_index {
          position: absolute;
          top: 0;
          bottom: 0;
          left: 20px;
          line-height: 96px;
          margin: auto;
          font-family: Lora;
          font-size: 56px;
          color: rgba(255,255,255,.2)
      }
      .question_title {
          color: #fff;
          font-size: 14px;
          display: table-cell;
          height: 92px;
          vertical-align: middle;
          line-height: 25px;
          font-weight: 500;
          font-family: "PingFang SC"
      }
      .question_bd {
          padding-bottom: 12px;
          min-height: 236px;
      }
      .question_answer {
          position: relative;
          display: block;
          height: 56px;
          padding: 0 32px;
          font-size: 16px;
          overflow: hidden;
          color: #5d646e;
          &.success {
            color: #2990e4;
          }
      }
      .question_answer_bg {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          background: rgba(0,0,0,.05);
          opacity: 0;
          -webkit-transform-origin: 100% 0;
          -moz-transform-origin: 100% 0;
          -ms-transform-origin: 100% 0;
          transform-origin: 100% 0;
          -webkit-transition: opacity .2s;
          -moz-transition: opacity .2s;
          -o-transition: opacity .2s;
          transition: opacity .2s;
      }
      .question_answer_text {
          position: relative;
          display: table-cell;
          height: 56px;
          vertical-align: middle;
          line-height: 28px;
      }
      .question_ft {
        font-size: 12px;
        color: #adb4be;
        line-height: 15px;
        top: 100%;
        margin-top: 16px 0;
      }
    }
    &.f7_intro {
      z-index: 102;
    }
    &.f7_result {
      z-index: 0;
    }
  }
}
.count-down{
  position: absolute;
  bottom: 0;
  /* top: 0; */
  left: 0;
  right: 0;
  height: 30%;
}
canvas {
  position: absolute;
  left: 50%;
  top: 50%;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}
.magicRotate {
  transform-origin: 100% 200%;
  transform: scale(1,1) rotate(-270deg);
  transition-duration: 2s;
  transition-duration: 2s;
}
.magic {
  animation-name: magic;
  animation-duration: 2s;
  animation-delay: 1s;
  animation-fill-mode: both
}
@-webkit-keyframes magic {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
@keyframes magic {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
@media screen and (max-height: 480px) {
  .f7-wrapper {
    .f7-page {
      .wbd_card {
        .question_hd {
          height: 70px;
          .question_index {
            line-height: 70px;
            font-size: 44px;
          }
          .question_title {
            height: 70px;
            font-size: 12px;
          }
        }
        .question_bd {
          min-height: 176px;
          .question_answer {
            height: 40px;
            .question_answer_text {
              height: 40px;
            }
          }
        }
        .intro_title {
          padding-top: 80px;
        }
      }
    }
  }
}
</style>
