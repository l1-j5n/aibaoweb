<template>
  <div id="userquestion" v-title="'用户中心'">
    <topBar :title="'常见问题'"></topBar>

    <div class="wrap">
      <div class="main" v-for="(item,index) in content" :key="index">
        <div class="question">
          <div class="wrapicon">
            <svg class="icon" aria-hidden="true">
              <use xlink:href="#icon-help"></use>
            </svg>
          </div>
          {{ item.ques }}
        </div>
        <div class="answer">
          <div class="wrapicon">
            <svg class="icon" aria-hidden="true">
              <use xlink:href="#icon-form"></use>
            </svg>
          </div>
          {{ item.answer }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import topBar from '@/components/TopBar'
import wx from 'weixin-js-sdk'
export default {
  name: 'UserQuestion',
  components: {
    topBar
  },
  data () {
    return {
      content: []
    }
  },
  mounted () {
    this.getInfo()
    this.$nextTick(() => {
      this.getWX()
    })
  },
  methods: {
    getInfo () {
      this.axios.get('/api2/getFaq').then(res => {
        this.content = res.data.content
      })
    },
    getWX () {
      let nowUrl = window.location.href
      this.axios.get('/api/getWxToken', {
        params: {
          url: nowUrl
        }
      }).then((response) => {
        sessionStorage.appId = response.data.appID
        sessionStorage.timestamp = response.data.timestamp
        sessionStorage.nonceStr = response.data.nonceStr
        sessionStorage.signature = response.data.signature
        let wxUrl = window.location.href.replace('#', '~')
        this.startWX(wxUrl)
      })
    },
    startWX (url) {
      wx.config({
        debug: false,
        appId: sessionStorage.appId,
        timestamp: sessionStorage.timestamp,
        nonceStr: sessionStorage.nonceStr,
        signature: sessionStorage.signature,
        jsApiList: ['checkJsApi', 'onMenuShareTimeline', 'onMenuShareAppMessage']
      })
      wx.ready(function () {
        wx.checkJsApi({
          jsApiList: ['onMenuShareTimeline', 'onMenuShareAppMessage']
        })
        wx.onMenuShareTimeline({
          title: 'AI保数据 | 常见问题', // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: 'AI保数据 | 常见问题', // 分享标题
          desc: 'AI保数据使用指南', // 分享描述
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
      })
      wx.error(function (res) {
        console.log(res)
      })
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
.main {
  margin: 10px auto 0;
  padding: 20px;
  border-bottom: 2PX solid #eee;
  background: #fff;
}

.wrapicon {
  position: absolute;
  top: 0;
  left: 0;
  width: 40px;
  height: 40px;
  line-height: 40px;
}
.icon {
  width: 30px;
  height: 30px;
  padding: 6px;
}
.question,
.answer {
  position: relative;
  padding-left: 58px;
  line-height: 45px;
}
.question {
  font-size: var(--m2-fs);
  color: #333;
  & .icon {
    color: var(--red);
  }
}
.answer {
  margin-top: 10px;
  font-size: var(--s-fs);
  color: #999;
  & .icon {
    color: var(--blue);
  }
}
</style>
