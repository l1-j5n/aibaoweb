<template>
  <div id="userclaims" v-title="'用户中心'">
    <topBar :title="'理赔指南'"></topBar>

    <div class="main">
      <div v-for="(item, index) in dataClaims" :key="index" class="list">
        <div class="img">
          <div :class="['icon','icon' + index ]"></div>
        </div>
        <div class="content">
          <div class="caption">{{ item.caption }}</div>
          <div class="info" ref="test">{{ item.info }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import topBar from '@/components/TopBar'
import wx from 'weixin-js-sdk'
export default {
  name: 'UserClaims',
  components: {
    topBar
  },
  data () {
    return {
      dataClaims: [
        {
          caption: '理赔报案',
          info: '拨打AI保客服热线 400-0015-006 免费代理报案及协助。'
        },
        {
          caption: '提交资料',
          info: 'AI保理赔服务专员将通过绿色理赔通道，帮助您向保险公司申请理赔。'
        },
        {
          caption: '保险公司审核',
          info: '我们将及时促进理赔进度并为您督促保险公司尽快结案。'
        },
        {
          caption: '赔款到账',
          info: '保险公司审核通过即可赔付。'
        }
      ]
    }
  },
  mounted () {
    this.$refs.test[0].innerHTML = '拨打AI保客服热线<span style="color:#69c;"> 400-0015-006 </span>免费代理报案及协助。'
    this.$nextTick(() => {
      this.getWX()
    })
  },
  methods: {
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
          title: 'AI保数据 | 理赔指南', // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: 'AI保数据 | 理赔指南', // 分享标题
          desc: '全程协助，轻松理赔', // 分享描述
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
#userclaims {
  height: 100%;
}

.main {
  height: calc(100% - 170px);
  background: var(--bgc);
  padding-top: 80px
}

.list {
  display: flex;
  position: relative;
  width: 670px;
  margin: 0 auto 120px;
  &:after {
    content: "";
    position: absolute;
    bottom: -100px;
    left: 75px;
    width: 80px;
    height: 80px;
    background-image: url("../../assets/img/ico-d.png");
    background-repeat: repeat-y;
    background-position: 0 0;
  }
  &:last-child:after {
    content: normal;
  }
  & .img {
    flex: 1;
    & .icon {
      height: 150px;
      width: 150px;
      background: var(--s-blue);
      border-radius: 50%;
      background-size: 50% 50%;
      background-position: center center;
      background-repeat: no-repeat;
    }
    & .icon0 {
      background-image: url("../../assets/img/claim0.png");
    }
    & .icon1 {
      background-image: url("../../assets/img/claim1.png");
    }
    & .icon2 {
      background-image: url("../../assets/img/claim2.png");
    }
    & .icon3 {
      background-image: url("../../assets/img/claim3.png");
    }
  }
  & .content {
    flex: 3;
    margin-left: 20px;
    & .caption {
      color: var(--blue);
      font-size: var(--m-fs);
    }
    & .info {
      color: var(--m-bgc);
      font-size: var(--s-fs);
      margin-top: 10px;
    }
  }
}
</style>
