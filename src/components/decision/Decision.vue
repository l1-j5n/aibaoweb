<template>
  <div id="decision" v-title="'定制'">
    <div class="banner">
      <div class="btn1" @click="start">开始定制</div>
      <div class="btn2" @click="saveList">已保存的方案</div>
    </div>
  </div>
</template>

<script>
import wx from 'weixin-js-sdk'
export default {
  name: 'Decision',
  mounted () {
    this.getWX()
  },
  methods: {
    start () {
      this.$router.push({ name: 'DecisionStep' })
    },
    saveList () {
      this.$router.push({ name: 'DecisionList' })
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
        this.startWX()
      })
    },
    startWX () {
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
          title: '为您定制 | 为您和家人定制个性化的保障方案。', // 分享标题
          link: '', // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: '为您定制 | 为您和家人定制个性化的保障方案。', // 分享标题
          desc: '全网选保，精准推荐，根据个性化需求，从全市场选择最适合您、最具性价比的产品。', // 分享描述
          link: '', // 分享链接
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
#decision {
  height: 100%;
}

.banner {
  background: url('../../assets/img/decision-bg.jpg');
  background-position: center top;
  background-size: cover;
  width: 100%;
  height: 100%;
  position: relative;
  & div {
    position: absolute;
    height: 100px;
    line-height: 100px;
    width: 60%;
    border-radius: 50px;
    text-align: center;
    left: 20%;
    font-size: var(--m-fs);
  }
}

.btn1 {
  background: rgba(102,153,204,.9);
  bottom: 22%;
  color: #fff;
}

.btn2 {
  background: hsla(0,0%,100%,.9);
  bottom: 12%;
  color: var(--blue);
}
</style>
