<template>
  <div id="app">
    <div class="f7-wrapper">
      <div class="f7-page f7_intro" :class="introStyle" v-show="introVisible">
        <div class="wbd_card">
        	<div class="intro_hd">
        		<h1 class="intro_title">
        			音乐猜猜猜
        		</h1>
        		<p class="intro_desc">
        			新歌不断
        			<br>
        		</p>
        	</div>
        	<div class="intro_bd">
        		<p class="intro_slogan">
        			“快问快打大考验”
        			<br>
        			快来测测你的音乐水平！
        		</p>
        		<a ontouchstart="" href="javascript:" class="intro_start wbd_btn" @click="start">
        			我要测
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
              <a class="question_answer" ontouchstart="" href="javascript:" @click="answerQuestion(subItem.type, item.success, item.ID)" v-for="(subItem, index) in item.questions" data-idx="0">
                <span class="question_answer_bg"></span>
                <span class="question_answer_text">{{subItem.type}}    {{subItem.text}}</span>
              </a>
            </div>
            <span class="question_ft"> {{ index }} / {{ pagesLen }}</span>
          </div>
        </div>
      </div>
      <div class="questions-result">
        <div class="f7-page f7_result" v-show="resultVisible" :class="resultStyle">
          <div class="wbd_card">
          	<div class="intro_hd">
          		<h1 class="intro_title">
          			测试分数
          		</h1>
          		<p class="intro_desc">
          		</p>
          	</div>
          	<div class="intro_bd">
          		<p class="intro_slogan">
          			“我的分数”
          			<br>
          			{{cacheCorrect.length}}分
          		</p>
          		<a ontouchstart="" href="" class="intro_start wbd_btn">
          			关注我
          		</a>
          	</div>
          </div>
        </div>
      </div>
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
      resultStyle: '',
      pages: [],
      cache: [],
      pagesLen: 0,
      cacheCorrect: [],
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
          })
          self.$set(self, 'pages', result.data)
          self.pagesLen = result.data.length
          self.lastId = result.data[self.pagesLen - 1].ID
          console.log(self.lastId)
        }
      })
      .catch(function (error) {
        console.log(error)
      })
    },
    start () {
      this.introStyle = 'magicRotate'
      // this.introVisible = false
    },
    answerQuestion (currentId, realId, id) {
      if (currentId === realId) {
        // 缓存答案
        this.cacheCorrect.push('true')
      }
      // 将数组第一个元素放到最后
      let selectData = this.pages.find(function (item) {
        return item.ID === id
      })
      selectData.styObj = 'magicRotate'
      this.$set(self, 'pages', this.pages)
      // 最后一个元素，选完打卡下班
      if (id === this.lastId) {
        this.resultStyle = 'magic'
      }
      // let remove_element = this.pages.shift()
      // this.pages.push(remove_element)
    }
  }
}
</script>

<style lang="less">
@import "./assets/less/index.less";
#app {
  min-width: 320px;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  padding-bottom: 0;
  font-size: 14px;
  overflow: hidden;
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
        padding: 4px 24px 0 56px;
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
          font-size: 16px;
          display: table-cell;
          height: 92px;
          vertical-align: middle;
          line-height: 25px;
          font-weight: 500;
          font-family: "PingFang SC"
      }
      .question_bd {
          padding-bottom: 12px
      }
      .question_answer {
          position: relative;
          display: block;
          height: 56px;
          padding: 0 32px;
          font-size: 16px;
          overflow: hidden;
          color: #5d646e;
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
.magicRotate {
  transform-origin: 100% 200%;
  transform: scale(1,1) rotate(-270deg);
  transition-duration: 2s;
  transition-duration: 2s;
}
.magic {
  animation-name: magic;
  animation-duration: 2s;
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
</style>
