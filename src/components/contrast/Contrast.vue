<template>
  <div id="contrast" v-title="'比价'">
    <!-- 顶部提示 -->
    <tips @showMask="showCP"></tips>

    <div class="wrap" v-if="life.list">
      <div class="caption">
        寿险<span @click="link(1)">更多产品></span>
      </div>
      <div class="content">
        <div class="list" v-for="(item,index) in life.list" :key="index" @click="proLink(item.id, item.cpid, item.be)">
          <div class="main">
            <div class="pic">
              <img :src="item.pic" alt="">
              <div v-if="item.buy_status != 0" class="sale"></div>
            </div>
            <div class="info">
              <div class="name">{{ item.name }}</div>
              <div class="label">{{ item.labels }}</div>
            </div>
            <div class="round">
              <div class="circle">
                <x-circle :percent="item.CP - 2" :stroke-width="7" :trail-width="7"
                stroke-color="#8db7e1" trail-color="#D9ECFF">
                  <span>{{ item.CP }}<br>CP</span>
                </x-circle>
              </div>
              <div class="price">
                ￥{{ item.price }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="wrap" v-if="disease.list">
      <div class="caption">
        重疾<span @click="link(2)">更多产品></span>
      </div>
      <div class="content">
        <div class="list" v-for="(item,index) in disease.list" :key="index" @click="proLink(item.id, item.cpid, item.be)">
          <div class="main">
            <div class="pic">
              <img :src="item.pic" alt="">
              <div v-if="item.buy_status != 0" class="sale"></div>
            </div>
            <div class="info">
              <div class="name">{{ item.name }}</div>
              <div class="label">{{ item.labels }}</div>
            </div>
            <div class="round">
              <div class="circle">
                <x-circle :percent="item.CP - 2" :stroke-width="7" :trail-width="7"
                stroke-color="#8db7e1" trail-color="#D9ECFF">
                  <span>{{ item.CP }}<br>CP</span>
                </x-circle>
              </div>
              <div class="price">
                ￥{{ item.price }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <tabBar></tabBar>

    <!-- 遮罩层 -->
    <actionsheet v-model="mask" :menus="nodata"
    @on-click-mask="colseMask"></actionsheet>

    <!-- cp值解释框 -->
    <div class="infoMask" v-if="infoShow">
      <div class="close" @click="colseMask">
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-close"></use>
        </svg>
      </div>
      <div class="warp">
        <div class="info" v-for="(item,index) in cpdata" :key="index">
          <div class="title">{{ item.caption }}</div>
          <div class="main">{{ item.info }}</div>
          <div class="main2">{{ item.info2 }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import tips from '@/components/Tips'
import tabBar from '@/components/TabBar'
import { XCircle, Actionsheet } from 'vux'
import wx from 'weixin-js-sdk'
export default {
  name: 'Contrast',
  components: {
    tips,
    tabBar,
    XCircle,
    Actionsheet
  },
  data () {
    return {
      life: [],
      disease: [],
      mask: false,
      infoShow: false,
      nodata: [],
      cpdata: [{
        caption: '什么是CP值？',
        info: 'CP是Cost Performance（性价比）的简称，CP值即为性价比分值。CP值是AI保数据独家发布、业内首创的产品评测标准。是我们的精算师团队使用精算定价模型，根据您的个人信息及投保选项，综合考虑产品的保障责任、赔付细则，对产品进行的综合评分，反应了当前选项下该产品的性价比。',
        info2: 'CP值、对冲补偿收益率、产品雷达图，三者共同构建了AI保数据的保险产品评测体系。用量化、直观、可视化的方式为您解读保险产品。对冲补偿收益率趋势图，可以在www.aibaodata.com上查看。'
      }]
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
      this.axios.get('/api2/getContrast').then(res => {
        this.life = res.data.content[0]
        this.disease = res.data.content[1]
      })
    },
    link (type) {
      this.$router.push({ name: 'ContrastList', params: {type: type} })
    },
    proLink (id, cpid, be) {
      this.$router.push({ name: 'ProductDetail', params: {id: id, cpid: cpid, be: be, maxcp: true} })
    },
    showCP () {
      this.mask = true
      this.infoShow = true
    },
    // 关闭遮罩层
    colseMask () {
      this.mask = false
      this.infoShow = false
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
          title: 'AI比价 | 在这儿轻松看懂产品性价比，节省一个亿。', // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: 'AI比价 | 在这儿轻松看懂产品性价比，节省一个亿。', // 分享标题
          desc: '哪个产品性价比高？保障多少年好？缴费期选几年？看CP值清晰明了。', // 分享描述
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
#contrast {
  height: calc(100% + 550px);
}

.wrap {
  margin-bottom: 20px;
}

.caption {
  display: flex;
  justify-content: space-between;
  padding: 0 20px;
  background: #fff;
  height: 80px;
  line-height: 80px;
  font-size: var(--m-fs);
  color: var(--m-bgc);
  & span {
    font-size: var(--m2-fs);
  }
}

.content {
  border-top: 2PX solid #eee;
}

.list {
  background: #fff;
  padding: var(--edge);
  border-bottom: 2PX solid #eee;
  & .main {
    display: flex;
  }
  & .pic {
    flex: 1;
    position: relative;
    width: 170px;
    height: 170px;
    & img {
      width: 100%;
      height: 100%;
      border: 2PX solid #eee;
    }
    & .sale {
      position: absolute;
      left: 0;
      top: -10px;
      width: 160px;
      height: 80px;
      background-image: url("../../assets/img/sale.png");
      background-size: 50%;
      background-repeat: no-repeat;
    }
  }
  & .info {
    flex: 2;
    margin-left: var(--edge);
    & .name {
      font-size: var(--m2-fs);
      min-height: 80px;
      line-height: 40px;
    }
    & .label {
      font-size: var(--s-fs);
      color: var(--s-bgc);
      height: 80px;
      line-height: 40px;
    }
  }
  & .round {
    flex: 1;
    & .price {
      font-size: var(--m3-fs);
      margin-top: 10px;
      margin-right: 10px;
    }
    & .circle {
      width: 120px;
      height: 120px;
      font-size: var(--m-fs);
      color: var(--blue);
      float: right;
    }
  }
}

/* 明细弹出层 */
.infoMask {
  z-index: 9999;
  max-height: 90vh;
  height: auto;
  width: 90%;
  background: var(--bgc);
  border-radius: 20px;
  position: fixed;
  overflow: hidden;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%,-50%,0);
  & .warp {
    max-height: 80vh;
    overflow-y: scroll;
    margin-top: 60px;
    backface-visibility: hidden;
  }
  & .title {
    font-size: var(--b-fs);
    color: var(--d-bgc);
    text-align: center;
    padding: var(--edge) 0;
  }
  & .main {
    font-size: var(--b-fs);
    color: var(--s-bgc);
    padding-bottom: 40px;
  }
  & .main2 {
    font-size: var(--b-fs);
    color: var(--s-bgc);
    padding-bottom: 40px;
    margin-top: -30px;
  }
  & .info {
    width: 90%;
    margin: 0 auto;
  }
  & .info:not(:last-child) {
    border-bottom: 1PX solid #eee;
  }
  & .close {
    position: absolute;
    width: 100%;
    height: 50px;
    & .icon {
      width: 40px;
      height: 40px;
      color: var(--s-bgc);
      float: right;
      margin-right: 15px;
      margin-top: 15px;
    }
  }
}
</style>
