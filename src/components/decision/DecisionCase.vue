<template>
  <div id="decisioncase" v-title="'定制'" v-if="lazy">
    <!-- 过期后的顶部提示 -->
    <div class="overdue" v-if="!content.is_expired">
      <svg class="icon" aria-hidden="true">
        <use xlink:href="#icon-warning"></use>
      </svg>
      <p class="tip">按照（{{userName}}）当前年龄，本保障方案已失效</p>
    </div>
    <!-- 右侧菜单 -->
    <div v-show="rightShow" class="divCenter" align="center" @click.self="rightShow = !rightShow">
      <transition name="fade">
        <div v-show="rightShow" class="rightNav">
          <div class="rightNav-title">
            其他家庭成员
          </div>
          <div class="rightNav-user" v-for="(item,index) in roles" :key="index">
            <img :src="item.img" alt="" @click="setRole(index,(item.typename))">{{item.typename}}
            <svg class="icon" :class="{ishow:index==iconShow}" aria-hidden="true">
              <use xlink:href="#icon-Selected"></use>
            </svg>
          </div>
        </div>
      </transition>
    </div>
    <!-- header -->
    <div class="header">
      <div class="top">
        <div class="headerBtn">
          <button @click="confirmShow = true" v-if="issub!=1&&isself">
            <svg class="icon" aria-hidden="true">
              <use xlink:href="#icon-save"></use>
            </svg>
            保存方案</button>
          <button v-if="issub==1&&isself">
            <svg class="icon" aria-hidden="true">
              <use xlink:href="#icon-saved"></use>
            </svg>
            已保存</button>
          <button @click="rightShow = !rightShow" class="button2">
            <svg class="icon" aria-hidden="true">
              <use xlink:href="#icon-change"></use>
            </svg>
            其他成员</button>
        </div>
        <img :src="content.headimg" class="portrait" alt="">
        <div class="sumPrice">
          最佳方案总价：￥{{content.price}}
        </div>
      </div>
      <div class="userInfo">
        <span>{{userName}}</span>
        <div v-for="(item,index) in content.labels" :key="index">{{item}}</div>
      </div>
    </div>

    <confirm show-input :input-attrs="{type: 'text'}" v-model="confirmShow" :title="'请输入方案名'" @on-confirm="onConfirm"></confirm>
    <alert v-model="showAlert" :title="'提示'" :content="alertContent"></alert>

    <!-- 推荐前言 -->
    <div v-if="content.recommendInfo" class="recommend">
      <p class="recommend-title">AI保智能投保顾问推荐方案</p>
      <div class="recommend-info">
        <p>综合考虑被评测人的个人基本信息、家庭财务状况，结合其生活习惯信息，建议配置以下板块的保险产品：<br/></p>
        <p v-if="content.recommendInfo[0].length>0">
          <span>{{content.recommendInfo[0]}}</span>板块保障财务风险
          <label v-if="content.recommendInfo[1].length<1">。</label>
          <label v-else>，</label>
        </p>
        <p v-if="content.recommendInfo[1].length>0">
          <span>{{content.recommendInfo[1]}}</span>板块保障身体健康风险
          <label v-if="content.recommendInfo[2].length<1">。</label>
          <label v-else>，</label>
        </p>
        <p v-if="content.recommendInfo[2].length>0">补充
          <span>{{content.recommendInfo[2]}}</span>板块保障旅游出行风险。</p>
      </div>
    </div>

    <!-- 主要内容 -->
    <div class="main">
      <div class="list" :class="{ pullUp: !showAll[index].show }" v-for="(item,index) in content.cases" :key="index" v-if="item.list.length>0">
        <div class="list-top" :class="{list_top_faile: !content.is_expired}">
          <div class="case-name">{{item.name}}</div>
          <div class="tips">
            <div class="tip-left">
              <div style="font-size:0.389rem;">
                <div class="wrap-stars">
                  <div class="bgstars"></div>
                  <div class="stars" :style="'width:'+item.star/0.05+'%'"></div>
                </div>
              </div>
              <div>投保优先级</div>
            </div>
            <div class="tip-right">
              <div style="font-size:0.389rem;">{{item.list[0].be}}</div>
              <div>建议投保额</div>
            </div>
          </div>
        </div>
        <div class="list-content">
          <div class="column">
            <div class="column-title">
              <svg class="icon" aria-hidden="true">
                <use xlink:href="#icon-fengxianpingce"></use>
              </svg>
              风险解读
            </div>
            <div class="column-text" :class="{ textPullDown: !showAll[index].show }">
              {{item.tips[0]}}
            </div>
          </div>
          <div v-if="showAll[index].show==false" class="pullDown" @click="show(index)">
            <span>查看详情</span>
            <div>
              <svg class="icon" aria-hidden="true">
                <use xlink:href="#icon-moreDown"></use>
              </svg>
            </div>
          </div>
          <div class="column">
            <div class="column-title">
              <svg class="icon" aria-hidden="true" style="font-size: 0.5rem;">
                <use xlink:href="#icon-jianyi"></use>
              </svg>
              投保建议
            </div>
            <div class="column-text">
              {{item.tips[1]}}
            </div>
          </div>

          <div class="content" v-for="(item2,index2) in item.list" :key="index2" @click="router(item2.planid,item2.pvid)">
            <div class="content-img">
              <img :src="item2.logo" alt="" class="product-img">
              <div v-if="item2.buy_status!=0" class="sale"></div>
            </div>
            <div class="product-info">
              <div class="product-name">
                {{item2.caption}}
              </div>
              <div :class="{css:index2==0}"></div>
              <div class="product-tips">
                {{item2.labels}}
              </div>
              <div class="product-price">
                <span>￥</span>
                <span>{{item2.price}}</span>
              </div>
            </div>
          </div>

          <div class="column">
            <div class="column-title">
              <svg class="icon" aria-hidden="true">
                <use xlink:href="#icon-Prompt"></use>
              </svg>
              温馨提示
            </div>
            <div class="column-text">
              {{item.tips[2]}}
            </div>
          </div>
          <div class="shrink" @click="hide(index)">
            <div class="iconUp">
              <svg class="icon" aria-hidden="true">
                <use xlink:href="#icon-moreDown"></use>
              </svg>
            </div>
            <span>收起</span>
          </div>
        </div>
      </div>
    </div>

    <!-- 底部 -->
    <div class="foot">
      <p>注：保险配置预算为AI保-爱宝建议的最佳保障方案预算，如果您已有一定额度的保障，需自行扣除，根据您的实际需求做出调整。</p>
      <!-- <button type="button" name="button" @click="consultation">咨询保险专家</button> -->
    </div>
  </div>
</template>

<script>
import { Confirm, Alert } from 'vux'
import wx from 'weixin-js-sdk'
export default {
  name: 'DecisionCase',
  components: {
    Confirm,
    Alert
  },
  props: {
    id: {}
  },
  data () {
    return {
      lazy: false,
      confirmShow: false,
      showAlert: false,
      alertContent: '',
      rightShow: false,
      iconShow: 0,
      content: [], // 单个角色信息
      issub: '', // 是否已储存
      isself: '', // 是否是个人
      roles: [], // 所有角色列表
      roleId: '', // 单个角色ID
      userName: '', // 当前角色名称
      showAll: [],
      wxName: ''
    }
  },
  beforeRouteEnter (to, from, next) {
    if (from.name === 'DecisionList' || from.name === 'DecisionStep') {
      to.meta.isBack = false
    }
    next()
  },
  mounted () {
    this.getInfo()
    this.$nextTick(() => {
      this.getWX()
    })
  },
  activated () {
    if (!this.$route.meta.isBack) {
      this.getInfo()
      this.$nextTick(() => {
        this.getWX()
      })
    }
    this.$route.meta.isBack = true
  },
  methods: {
    getInfo () {
      this.axios.get('/api2/Decision/getResultRoles', {
        params: {
          id: this.id
        }
      }).then(res => {
        this.roles = res.data.roles
        this.roleId = this.roles[0].roleid
        this.isself = res.data.isself
        this.issub = res.data.issub
        this.userName = this.roles[0].typename
      }).then(() => {
        this.getPersonInfo()
      })
    },
    getPersonInfo () {
      this.axios.get('/api2/Decision/getResult', {
        params: {
          id: this.id,
          roleid: this.roleId
        }
      }).then(res => {
        this.content = res.data.content
        let temShowList = []
        this.content.cases.forEach(() => {
          temShowList.push({ show: false })
        })
        this.showAll = temShowList
      }).then(() => {
        this.lazy = true
      })
    },
    show (n) {
      this.showAll[n].show = true
    },
    hide (n) {
      this.showAll[n].show = false
    },
    setRole (n, name) {
      this.iconShow = n
      this.rightShow = !this.rightShow
      this.roleId = this.roles[n].roleid
      this.userName = this.roles[n].typename
      this.getPersonInfo()
    },
    router (a, b) {
      this.$router.push({ name: 'ProductDetail', params: { id: a, pvid: b } })
    },
    onConfirm (value) {
      if (value === '') {
        this.showAlert = true
        this.alertContent = '方案名不能为空'
      } else {
        this.axios.post('/api2/Decision/subResult', {
          id: this.id,
          name: value
        }).then(res => {
          if (res.data.msg === '保存成功') {
            this.showAlert = true
            this.alertContent = '保存成功'
            this.issub = 1
          }
        })
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
        let wxUrl = window.location.href.replace('#', '~')
        this.axios.get('/api/getUser').then(res => {
          this.startWX(res.data.content.name, wxUrl)
        })
      })
    },
    startWX (name, url) {
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
          title: '为您定制 | 看看' + name + '在AI保定制的保障方案', // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: '为您定制 | 看看' + name + '在AI保定制的保障方案', // 分享标题
          desc: '智能顾问-爱宝给我定制的保险方案，既全面又不贵，我看靠谱。', // 分享描述
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
.header {
  height: 328px;
  & .top {
    height: 200px;
    width: 100%;
    position: relative;
    background-size: cover;
    background-image: url("../../assets/img/newdecision-banner.jpg");
    background-position: center center;
    & .headerBtn {
      width: 100%;
      & button {
        border: none;
        background-color: inherit;
        padding: 30px;
        color: #fff;
        font-size: var(--m2-fs);
        font-weight: bold;
      & .icon {
          display: inline-block;
          font-size: 18px;
          padding-bottom: 5px;
          padding-right: 1px;
        }
      }
      & .button2 {
        float: right;
        padding: 30px 30px 30px 40px;
      }
    }
    & .sumPrice {
      color: #fff;
      font-size: var(--price-fs);
      position: absolute;
      left: 224px;
      bottom: 20px;
    }
    & .portrait {
      width: 184px;
      height: 184px;
      position: absolute;
      left: 20px;
      top: 104px;
      border-radius: 50%;
      box-shadow: 0px 0px 32px 0px rgba(102, 153, 204, 0.42);
    }
  }
  & .userInfo {
    padding-left: 224px;
    padding-top: 30px;
    & span {
      color: var(--blue);
      font-size: var(--m2-fs);
      padding-right: 16px;
    }
    & div {
      height: 35px;
      border-radius: 28px;
      border: 1PX solid #ff5f57;
      display: inline-block;
      text-align: center;
      color: #ff4b38;
      margin-right: 16px;
      padding: 0 12px;
    }
  }
}

.recommend {
  border: 1PX solid #ccc;
  border-radius: 28px;
  margin: 0 20px;
  position: relative;
  & .recommend-title {
    text-align: center;
    font-size: var(--b-fs);
    color: var(--d-bgc);
    padding-top: 32px;
    position: relative;
  }
  & .recommend-title:after {
    position: absolute;
    height: 0;
    content: "";
    width: 80px;
    border-bottom: 4PX solid #f97474;
    top: 100px;
    left: calc(50% - 40px);
  }
  & .recommend-info {
    font-size: var(--m2-fs);
    padding: 50px 30px 32px;
    & span {
      color: #6699cc;
      font-weight: bold;
    }
    & p {
      display: inline;
    }
  }
}
.recommend:after,
.recommend:before {
  border: solid transparent;
  content: "";
  height: 0;
  position: absolute;
  width: 0;
  left: calc(50% - 30px);
  border-width: 30px;
  border-bottom-width: 16px;
}
.recommend:after {
  border-bottom-color: #f8f8f8;
  top: -44px;
}
.recommend:before {
  border-bottom-color: #ccc;
  top: -46px;
}

.main {
  padding: 40px 20px;
  & .pullUp {
    height: 440px;
    overflow: hidden;
  }
  & .pullDown {
    color: #6699cc;
    text-align: center;
    padding-bottom: 50px;
  }
  & .list {
    background-color: #fff;
    margin-bottom: 40px;
    box-shadow: 0px 20px 32px 0px rgba(123, 159, 195, 0.24);
    border-radius: 10px;
    overflow: hidden;
    & .list-top {
      height: 120px;
      background-size: cover;
      background-image: url("../../assets/img/newdecision-title.png");
      color: #fff;
      padding: 0 30px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      & .case-name {
        font-size: var(--m-fs);
      }
      & .tips {
        font-size: var(--s-fs);
        width: 300px;
        display: flex;
        justify-content: space-between;
        text-align: center;
        & .tip-left {
          position: relative;
        }
        & .tip-left:after {
          position: absolute;
          height: 70px;
          content: "";
          width: 0;
          border-right: 1PX solid #fff;
          top: 0px;
          right: -15px;
        }
      }
    }
    & .list_top_faile {
      background-color: #c1c1c1;
      background-image: none;
    }
    & .list-content {
      padding-top: 44px;
      & .column {
        padding: 20px;
        & .column-title {
          color: var(--d-bgc);
          font-size: var(--m-fs);
          padding-bottom: 24px;
          & .icon {
            display: inline-block;
            padding-bottom: 3px;
            padding-right: 2px;
            color: #ff6600;
            width: 45px;
            height: 45px;
          }
        }
        & .column-text {
          color: #777;
          font-size: var(--s-fs);
        }
        & .textPullDown {
          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-line-clamp: 2;
          -webkit-box-orient: vertical;
        }
      }
      & .content {
        padding-bottom: 10px;
        margin: 20px;
        display: flex;
        justify-content: space-between;
        border-bottom: 1PX solid #eee;
        & .content-img {
          position: relative;
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
        & .product-img {
          width: 188px;
          height: 188px;
          margin-right: 28px;
          flex: 6;
          box-shadow: 0 0 8px 0px rgba(100, 183, 224, 0.27);
        }
        & .product-info {
          position: relative;
          flex: 16;
          & .product-name {
            font-size: var(--m2-fs);
            color: #000;
            line-height: 40px;
            padding-bottom: 20px;
          }
          & .css {
            position: absolute;
            right: 60px;
            top: 40px;
            width: 120px;
            height: 120px;
            background-image: url("../../assets/img/good2.png");
            background-size: 100%;
            opacity: 0.7;
          }
          & .product-tips {
            font-size: var(--s-fs);
            color: var(--s-bgc);
            padding-bottom: 20px;
            & span {
              position: relative;
              padding-right: 5px;
            }
          }
          & .product-price {
            float: right;
            padding-right: 14px;
            font-size: var(--s-fs);
            color: #777;
            & span:nth-child(1) {
              font-size: var(--s-fs);
              color: #ff5f57;
            }
            & span:nth-child(2) {
              font-size: var(--price-fs);
              color: #ff5f57;
            }
          }
        }
      }
      & .content:nth-of-type(1) {
        border: 1PX solid #f00 !important;
        label {
        }
      }
      & .shrink {
        font-size: var(--s-fs);
        color: #ccc;
        text-align: center;
        padding: 0 20px 20px;
        & .iconUp {
          transform: rotate(180deg);
        }
      }
    }
  }
}

.foot {
  width: 100%;
  padding-bottom: 20px;
  & p {
    padding: 0 40px 40px;
    font-size: var(--m3-fs);
    color: var(--m-bgc);
  }
  & button {
    box-shadow: 0 5px 15px #c1dcf5;
    border-radius: 34px;
    border: 1px solid #77b1ea;
    background-color: inherit;
    color: #6699cc;
    padding: 11px 72px;
    margin: 0 auto;
    display: block;
    font-size: 13px;
  }
}

.divCenter {
  position: fixed;
  z-index: 9;
  background-color: rgba(52, 52, 52, 0.8);
  height: 100%;
  width: 100%;
  & .rightNav {
    overflow-y: auto;
    float: right;
    width: 260px;
    height: 100%;
    background: linear-gradient(#1fd6d8, #4c96e8, #6a83ea);
    box-shadow: 0 10px 32px 0 rgba(17, 48, 66, 0.53);
    color: #fff;
    text-align: center;
    padding-bottom: 40px;
    & .rightNav-title {
      font-size: var(--m-fs);
      padding-top: 50px;
    }
    & .rightNav-user {
      font-size: var(--s-fs);
      padding-top: 26px;
      position: relative;
      & img {
        height: 108px;
        width: 108px;
        border-radius: 50%;
        display: block;
        margin: 0 auto;
        margin-bottom: 10px;
      }
      & .icon {
        display: none;
      }
      & .ishow {
        display: block;
        position: absolute;
        top: 50%;
        right: 25%;
        color: #FFB83E;
        width: 40px;
        height: 40px;
      }
    }
    & .rightNav-user:last-child {
      padding-bottom: 26px;
    }
  }
}

.fade-enter-active {
  transition: all 0.4s;
}
.fade-enter {
  opacity: 0;
  transform: translateX(260px);
}
.fade-leave-active {
  transition: all 2.4s;
}
.fade-leave-to {
  opacity: 0;
  transform: translateX(260px);
}

.wrap-stars {
  position: relative;
  margin: 10px 0;
  width: 110px;
  height: 22px;
}
.bgstars {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-image: url("../../assets/img/stars.png");
  background-size: auto 95%;
  background-repeat: no-repeat;
  background-position: 0 0;
}
.stars {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-image: url("../../assets/img/stars3.png");
  background-size: auto 95%;
  background-repeat: no-repeat;
  background-position: 0 0;
}

.overdue {
  background-color: #ffdfdf;
  height: 80px;
  & .icon {
    color: #ff5847;
    padding: 0 20px;
    margin-top: -8px;
  }
  & .tip {
    display: inline;
    font-size: var(--m-fs);
    line-height: 84px;
  }
}
</style>
