<template>
  <div id="product-detail" v-title="'产品详情'">
    <topBar :title="content.name"></topBar>
    <loading :show="showLoading" text="" :position="'absolute'"></loading>
    <!-- banner图 -->
    <div class="banner-detail">
      <div class="wrap-content">
        <div class="text">
          <div class="title">{{ content.name }}</div>
          <div class="labels">{{ content.labels }}</div>
        </div>
        <div class="cp" v-if="content.cp">
          <div class="circle">
            <x-circle :percent="parseInt(content.cp)" :stroke-width="7" :trail-width="7"
            stroke-color="#D9ECFF" trail-color="#8DB7FF">
              <span>{{ content.cp }}<br>CP</span>
            </x-circle>
          </div>
        </div>
      </div>
    </div>
    <!-- 公司logo -->
    <div class="tip">
      <div>
        由<span>{{ content.comp_desc }}</span>承保并负责理赔
      </div>
      <img :src="img" alt="" width="30%" height="40%">
    </div>
    <!-- 主要内容 -->
    <div class="box">
      <!-- 下拉框 -->
      <div class="select-wrap" v-if="content.plans">
        <select v-model="planValue" @change="changePlan">
          <option v-for="(item,index) in content.plans" :key="index"
          :value="item.value">{{ item.name }}</option>
        </select>
      </div>
      <!-- 保障内容 -->
      <div class="terms">
        <div v-for="(item, index) in content.protectinfo" :key="index" class="wrap">
          <div>
            <div class="caption">{{ item.proname }}</div>
            <!-- <div class="label" v-if="!item.children[0].desc">{{ item.children[0].value }}</div> -->
            <div class="label" v-if="!item.moneys[0].desc && !isNaN(item.moneys[0].value)">{{ item.moneys[0].value*nums }}</div>
            <div class="label" v-if="!item.moneys[0].desc && isNaN(item.moneys[0].value)">{{ item.moneys[0].value }}</div>
            <svg class="icon" aria-hidden="true" v-if="item.moneys[0].desc">
              <use xlink:href="#icon-moreDown"></use>
            </svg>
          </div>
          <div v-if="item.moneys[0].desc" v-for="(item2, index2) in item.moneys" :key="index2" class="child">
            <div class="caption">{{ item2.desc }}</div>
            <!-- <div class="label">{{ item.price }}</div> -->
            <div class="label" v-if="!isNaN(item2.value)">{{ item2.value*nums }}</div>
            <div class="label" v-else>{{ item2.value }}</div>
          </div>
        </div>
      </div>
      <!-- 查看明细 -->
      <div class="detail" @click="detailMask">查看明细&nbsp;&nbsp;></div>
      <!-- 主要内容 -->
        <!-- 综合评分 -->
      <div class="modular">
        <div class="title">综合评分</div>
        <chart :options="polar" style="width:100%;height:280px;"></chart>
        <div class="evaluation" v-for="(item,index) in content.evaluations" :key="index">
          <div class="name">{{ item.name }}</div>
          <div class="info">{{ item.info }}</div>
        </div>
      </div>
        <!-- 常见问题 -->
      <div class="modular" v-if="content.question">
        <div class="title">常见问题</div>
        <div class="wrap-ques" v-for="(item,index) in content.question" :key="index">
          <div class="question">
            <div class="wrap-icon">
              <svg class="icon" aria-hidden="true">
                <use xlink:href="#icon-help"></use>
              </svg>
            </div>
            {{ item.ques }}
          </div>
          <div class="answer">
            <div class="wrap-icon">
              <svg class="icon" aria-hidden="true">
                <use xlink:href="#icon-form"></use>
              </svg>
            </div>
            <!-- {{ item.answer }} -->
            <div v-html="item.answer"></div>
          </div>
        </div>
      </div>
        <!-- 服务说明 -->
      <div class="modular" v-if="service.manpower||service.invoice">
        <div class="title">服务说明</div>
        <div class="server" v-if="service.manpower">
          <svg class="icon" aria-hidden="true">
            <use xlink:href="#icon-success"></use>
          </svg>
          <p>支持人工核保</p>
          <span>不符合健康告知可以提供体检材料，人工审核通过也可投保。</span>
        </div>
        <div class="server" v-if="service.invoice">
          <svg class="icon" aria-hidden="true">
            <use xlink:href="#icon-success"></use>
          </svg>
          <p>提供电子发票</p>
          <span>保单生效后可申请电子发票。</span>
        </div>
      </div>
        <!-- 相关文件 -->
      <div class="modular">
        <div class="title">相关文件</div>
        <div class="files">
          <a v-for="(item,index) in content.files" :key="index" :href="item.src"
          @click="fileMask(item)">
            {{ item.name }}
          </a>
        </div>
      </div>
    </div>
    <!-- 遮罩层 -->
    <actionsheet v-model="mask" :menus="filesMenu" @on-click-menu="fileLink"
    @on-click-mask="colseMask"></actionsheet>
    <!-- 明细弹出层 -->
    <div class="infoMask" v-if="infoShow">
      <div class="close" @click="detailMask">
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-close"></use>
        </svg>
      </div>
      <div class="warp">
        <div class="info" v-for="(item,index) in content.terms" :key="index">
          <div class="title">{{ item.caption }}</div>
          <div class="main">{{ item.info }}</div>
        </div>
      </div>
    </div>
    <!-- 投保or试算的按钮 -->
    <div class="foot">
      <div class="price">
        <span>{{ content.price | zero }}</span>&nbsp;{{ content.unit }}
      </div>
      <div class="btn-try" :class="{ btnbuy: content.saleStatus == 0 }" @click="openTry(0)">
        保费试算
      </div>
      <div class="btn-buy" v-if="content.saleStatus != 0" @click="openTry(1)">
        立即投保
      </div>
    </div>
    <!-- 保费试算 -->
    <div class="mask-try" v-if="showTry">
      <!-- 价格抬头 -->
      <div class="title">
        <div class="price">
          <p>总保费：</p>
          <span>{{ content.price | zero }}</span>&nbsp;{{ content.unit }}
        </div>
        <!-- <div class="btn-close" @click="closeTry">
          <svg class="icon" aria-hidden="true">
            <use xlink:href="#icon-close"></use>
          </svg>
        </div> -->
      </div>
      <!-- 主要内容 -->
      <div class="try-content">
        <tryForm @changePrice="changePrice" @changeNum="changeNum" :content="tryPrice" :id="id"></tryForm>
      </div>
      <!-- 底部按钮 -->
      <div class="try-foot" v-if="content.saleStatus != 0">
        <div class="l-btn" @click="closeTry">{{ leftBtn }}</div>
        <div class="r-btn" @click="insure">{{ rightBtn }}</div>
      </div>
      <div class="try-foot" v-else>
        <div class="one-btn" @click="closeTry">确定</div>
      </div>
    </div>
  </div>
</template>

<script>
import topBar from '@/components/TopBar'
import tryForm from '@/components/product/TryForm'
import { XCircle, Actionsheet, Loading } from 'vux'
import ECharts from 'vue-echarts'
import 'echarts/lib/chart/radar'
import wx from 'weixin-js-sdk'
export default {
  name: 'ProductDetail',
  components: {
    topBar,
    XCircle,
    Actionsheet,
    chart: ECharts,
    tryForm,
    Loading
  },
  props: {
    id: {},
    formIndex: {
      default: false
    }, // 判断是否从首页过来
    pvid: {
      default: 0
    },
    cpid: {
      default: 0
    },
    be: {
      default: 0
    },
    maxcp: {
      default: false
    }
  },
  data () {
    return {
      showLoading: false,
      content: [], // 所有数据
      tese: [], // 分享用的数据
      img: '', // 公司logo
      planValue: '', // 计划选项
      infoShow: false, // 是否显示详细信息
      service: '', // 服务说明
      filesMenu: [], // 多个相关文件时的弹出菜单
      mask: false, // 遮罩层
      showTry: false, // 是否显示保费试算
      leftBtn: '确定', // 保费试算底部按钮
      rightBtn: '立即投保', // 保费试算底部按钮
      tryPrice: [], // 保费试算的数据
      nums: 1, // 份数
      // 雷达图配置
      polar: {
        color: '#c00',
        radar: [{
          indicator: [0],
          radius: 90,
          center: ['50%', '50%'],
          name: {
            color: '#666',
            fontSize: 11
          }
        }],
        series: [{
          name: '综合评分',
          type: 'radar',
          data: [{
            value: [],
            areaStyle: {
              normal: {
                opacity: 0.1
              }
            }
          }],
          label: {
            show: true,
            color: '#c00',
            formatter: function (params) {
              return params.value
            }
          }
        }]
      }
    }
  },
  mounted () {
    this.getInfo(this.id)
    // this.$nextTick(() => {
    //   this.getWX()
    // })
  },
  methods: {
    // 获取所有信息
    getInfo (id) {
      this.showLoading = true
      this.axios.get('/api2/getProductInfo', {
        params: {
          ID: id,
          pvid: this.pvid,
          cpid: this.cpid,
          be: this.be,
          maxcp: this.maxcp
        }
      }).then(res => {
        this.name = res.data.content.name
        this.tese = res.data.content.tese
        this.content = res.data.content
        // 首页产品改为不可购买
        // if (!this.formIndex) {
        //   this.content.saleStatus = 0
        // }
        // 首页产品改为不可购买
        this.img = this.content.logoImg
        this.planValue = this.content.plansDefault
        this.service = this.content.service
        this.tryPrice = res.data.tryPrice
        // 增加份数
        if (this.tryPrice[0].id === 'nums') {
          this.nums = this.tryPrice[0].selected
        }
        // 转换雷达图数据
        let radarValue = []
        let max = {max: 100}
        let radarArr = res.data.content.radar
        for (let i = 0; i < res.data.content.radar.length; i++) {
          radarValue.push(res.data.content.radar[i].value)
          Object.assign(res.data.content.radar[i], max)
        }
        this.polar.series[0].data[0].value = radarValue
        this.polar.radar[0].indicator = radarArr
        this.showLoading = false
      }).then(() => {
        this.getWX()
      })
    },
    // 切换计划
    changePlan () {
      // this.getInfo(this.planValue)
      this.$router.replace({
        name: 'ProductDetail',
        params: {
          id: this.planValue
        }
      })
      this.getInfo(this.planValue)
    },
    // 查看明细
    detailMask () {
      this.filesMenu = []
      this.mask = !this.mask
      this.infoShow = !this.infoShow
    },
    // 相关文件存在多文件时弹出列表
    fileMask (item) {
      if (item.list.length === 1) {
        window.location.href = item.list[0].src
        return
      }
      this.mask = true
      let filesName = []
      for (var i = 0; i < item.list.length; i++) {
        let oneFiles = {label: '', value: ''}
        oneFiles.label = item.list[i].name
        oneFiles.value = item.list[i].src
        filesName.push(oneFiles)
      }
      this.filesMenu = filesName
    },
    // 相关文件的跳转
    fileLink (link) {
      window.location.href = link
    },
    // 关闭遮罩层
    colseMask () {
      this.mask = false
      this.infoShow = false
    },
    // 显示保费试算界面 0保费试算 1立即投保
    openTry (index) {
      if (index === 0) {
        this.showTry = true
        this.leftBtn = '确定'
        this.rightBtn = '立即投保'
      } else if (index === 1) {
        if (this.content.src) {
          window.location.href = this.content.src
        } else {
          this.showTry = true
          this.leftBtn = '返回'
          this.rightBtn = '下一步'
        }
      }
    },
    // 关闭保费试算
    closeTry () {
      this.showTry = false
      this.axios.post('/api2/updateProductInfo', {
        params: {
          ID: this.id,
          tryInfo: JSON.parse(localStorage.tryInfo)
        }
      }).then(res => {
        this.content.cp = res.data.content.cp
        this.content.terms = res.data.content.terms
        this.planValue = this.content.plansDefault
        this.tryPrice = res.data.tryPrice
        this.content.protectinfo = res.data.content.protectinfo
        // 转换雷达图数据
        let radarValue = []
        let max = {max: 100}
        let radarArr = res.data.content.radar
        for (let i = 0; i < res.data.content.radar.length; i++) {
          radarValue.push(res.data.content.radar[i].value)
          Object.assign(res.data.content.radar[i], max)
        }
        this.polar.series[0].data[0].value = radarValue
        this.polar.radar[0].indicator = radarArr
      })
    },
    // 保费试算更改价格
    changePrice (value) {
      this.content.price = value[0]
      this.content.unit = value[1]
      setTimeout(() => {
        this.axios.post('/api2/updateProductInfo', {
          params: {
            ID: this.id,
            tryInfo: JSON.parse(localStorage.tryInfo)
          }
        }).then(res => {
          this.tryPrice = res.data.tryPrice
        })
      }, 200)
    },
    changeNum (value) {
      this.nums = value
    },
    // 投保
    insure () {
      if (this.content.saleStatus === 1) {
        if (this.content.hasHealthInfo) {
          this.$router.push({ name: 'HealthInfo', params: { id: this.id } })
        } else {
          this.$router.push({ name: 'Order', params: { id: this.id, nums: this.nums } })
        }
      } else if (this.content.saleStatus === 2) {

      } else if (this.content.saleStatus === 3) {
        window.location.href = this.content.src
      }
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
        let desc = this.tese.join(',')
        let wxTitle = this.content.name
        let wxUrl = response.data.ref_url + this.id
        this.startWX(wxTitle, desc, wxUrl)
      })
    },
    startWX (name, desc, url) {
      let text = '保库'
      if (this.formIndex) { text = '严选' }
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
          title: 'AI' + text + ' | ' + name, // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: 'AI' + text + ' | ' + name, // 分享标题
          desc: desc + '。', // 分享描述
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
      })
      wx.error(function (res) {
        console.log(res)
      })
    }
  },
  filters: {
    // 保费为0时的显示
    zero (value) {
      if (value === '0.00') {
        return '--'
      } else {
        return value
      }
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
#product-detail {
  padding-bottom: 50px;
  /* height: 100%; */
}

/* .notouch {
  position: fixed;
} */

.banner-detail {
  width: 100%;
  height: 200px;
  background-image: url('../../assets/img/product-img.jpg');
  background-size: cover;
  background-position: center 0;
  color: #fff;
  & .wrap-content {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 auto;
    padding: 0 55px;
    height: 100%;
  }
  & .text {
    flex: 2.5;
    & .title {
      font-size: var(--b-fs);
      font-weight: 600;
      margin-bottom: 10px;
    }
    & .labels {
      font-size: var(--s-fs);
    }
  }
  & .cp {
    flex: 1;
    & .circle {
      float: right;
      width: 150px;
      height: 150px;
      font-size: var(--m-fs);
    }
  }
}

/* 公司logo */
.tip {
  display: flex;
  justify-content: space-between;
  padding: 16px 16px 16px 20px;
  background: var(--bgc);
  font-size: var(--m3-fs);
  & span {
    color: var(--blue);
  }
}

/* 主要内容外层div */
.box {
  padding: var(--edge);
}

/* 计划选项 */
.select-wrap {
  position: relative;
  background: #f0f5fa;
  margin-bottom: var(--edge);
  & select {
    appearance: none;
    display: block;
    width: 100%;
    height: 80px;
    line-height: 80px;
    padding: 0 34px;
    background: none;
    border: 0;
    color: var(--red);
    font-size: var(--m-fs);
    background-image: url("../../assets/img/down.png");
    background-repeat: no-repeat;
    background-position: 95%;
    background-size: 5%;
  }
  & select:focus {
    outline: none;
  }
}

/* 保障内容 */
.terms {
  color: var(--m-bgc);
  line-height: 2;
  & .wrap > div {
    display: flex;
    padding: 0 34px;
  }
  & .caption {
    flex: 2;
  }
  & .label {
    flex: 1;
    text-align: right;
  }
  & .wrap > div:first-child {
    background: var(--d-blue);
    font-size: var(--m3-fs);
    & .icon {
      width: 35px;
      height: 35px;
      margin-top: 10px;
    }
  }
  & .wrap .child {
    background: var(--s-blue);
    font-size: var(--s-fs);
  }
}
.terms .wrap:nth-child(1) > div:nth-child(1) {
  padding-top: 10px;
}
.terms .wrap:nth-last-child(1) > div:nth-child(1) {
  padding-bottom: 10px;
}

/* 查看明细 */
.detail {
  color: var(--blue);
  height: 80px;
  line-height: 80px;
  text-align: center;
  font-size: var(--m-fs);
  background: var(--s-blue);
  margin-top: 5px;
  margin-bottom: calc(var(--edge) * 2);
}

/* 详情包裹模块 */
.modular {
  margin-bottom: calc( 2 * var(--edge));
  /* 标题 */
  & .title {
    position: relative;
    text-indent: 40px;
    height: 80px;
    line-height: 80px;
    font-size: var(--m-fs);
    font-weight: 600;
    color: var(--d-bgc);
    background: var(--s-blue);
    padding-left: 5px;
  }
  & .title:after {
    content: "";
    position: absolute;
    left: var(--edge);
    top: 50%;
    margin-top: -6px;
    width: 16px;
    height: 16px;
    background: var(--blue);
    border-radius: 50%;
  }

  /* 综合评分 */
  & .evaluation {
    display: flex;
    font-size: var(--m3-fs);
    background: var(--s-blue);
    padding: var(--edge);
    & .name {
      flex: 1;
      color: var(--blue);
    }
    & .info {
      flex: 3;
      color: var(--s-bgc);
      text-align: justify;
    }
  }
  & .evaluation:nth-child(odd) {
    background: var(--d-blue);
  }
  & .evaluation:not(:first-child) {
    margin-bottom: 5px;
  }

  /* 常见问题 */
  & .wrap-ques {
    padding: var(--edge) 0;
    width: 90%;
    margin: 0 auto;
    & .wrap-icon {
      position: absolute;
      top: 0;
      left: 0;
      & .icon {
        width: 30px;
        height: 30px;
      }
    }
    & .question {
      position: relative;
      padding-left: 48px;
      line-height: 40px;
      font-size: var(--m2-fs);
      color: var(--d-bgc);
      & .icon {
        color: var(--red);
      }
    }
    & .answer {
      position: relative;
      padding-left: 48px;
      line-height: 40px;
      font-size: var(--s-fs);
      color: var(--s-bgc);
      margin-top: var(--edge);
      & .icon {
        color: var(--blue);
      }
    }
  }
  & .wrap-ques:not(:last-child) {
    border-bottom: 1PX solid #eee;
  }

  /* 服务说明 */
  & .server {
    padding: var(--edge) 0;
    width: 90%;
    margin: 0 auto;
    & .icon {
      color: #17c100;
      width: 30px;
      height: 30px;
    }
    & p {
      display: inline;
      color: var(--d-bgc);
      font-size: var(--m2-fs);
      margin-left: 7px;
    }
    & span {
      display: block;
      color: var(--s-bgc);
      font-size: var(--s-fs);
      padding-left: 44px;
    }
  }
  & .server:not(:last-child) {
    border-bottom: 1PX solid #eee;
  }

  /* 相关文件 */
  & .files {
    padding: var(--edge) 0;
    width: 90%;
    margin: 0 auto;
    font-size: var(--m2-fs);
    color: var(--blue);
    line-height: 60px;
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

/* 底部试算or投保 */
.foot {
  position: fixed;
  bottom: 0;
  height: 100px;
  line-height: 100px;
  width: 100%;
  background: var(--bgc);
  display: flex;
  border-top: 1PX solid #eee;
  text-align: center;
  & .price {
    flex: 1.2;
    text-indent: var(--edge);
    color: var(--l-bgc);
    & span {
      font-weight: bold;
    }
  }
  & .btn-try {
    flex: 1;
    color: var(--blue);
    text-decoration: underline;
    font-size: var(--b-fs);
  }
  & .btn-buy {
    flex: 1;
    color: #fff;
    font-size: var(--b-fs);
    background: var(--blue);
  }
  & .btnbuy {
    flex: 1;
    color: #fff;
    font-size: var(--b-fs);
    background: var(--blue);
    text-decoration: underline;
  }
}

/* 保费试算 */
.mask-try {
  z-index: 100;
  /* height: 100%; */
  background: var(--bgc);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  padding: var(--edge);
  & .title {
    display: flex;
    height: 80px;
    justify-content: space-between;
    border-bottom: 1PX solid #e5e5e5;
    & .price {
      color: var(--l-bgc);
      & p {
        display: inline-block;
        font-size: var(--b-fs);
      }
    }
    & .btn-close {
      margin-top: 10px;
      color: var(--s-bgc);
    }
  }
  & .try-content {
    margin-top: 40px;
    margin-bottom: 40px;
    overflow-y: scroll;
    height: calc(100% - 220px);
  }
  & .try-foot {
    display: flex;
    justify-content: space-between;
    position: fixed;
    left: var(--edge);
    right: var(--edge);
    bottom: var(--edge);
    & div {
      border: 1PX solid var(--blue);
      flex: 1;
      border-radius: 10PX;
      height: 80px;
      line-height: 80px;
      text-align: center;
      font-size: var(--m-fs);
    }
    & .l-btn {
      margin-right: var(--edge);
      color: var(--blue);
    }
    & .r-btn {
      background: var(--blue);
      color: #fff;
    }
    & .one-btn {
      background: var(--blue);
      color: #fff;
    }
  }
}
</style>
