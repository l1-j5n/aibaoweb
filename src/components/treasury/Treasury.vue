<template>
  <div id="treasury" v-title="'保库'">
    <!-- 顶部提示 -->
    <tips></tips>
    <!-- 搜索框 -->
    <search :results="results" v-model="searchValue" :auto-fixed="false"
      @on-change="searching" @on-cancel="hide"></search>
    <div class="loadmore-warp" v-show="searchOver">
      <div class="loadmore" ref="loadmorescroll">
        <div class="warp">
          <div class="list" v-for="(item,index) in results" :key="index" @click="toProduct(item.id)">
            <span>{{ item.name }}</span>
          </div>
          <div class="loading" v-show="loading"></div>
          <div class="end" v-show="end">没有更多产品啦</div>
          <div class="noRes" v-show="results.length == 0">没有搜索结果</div>
        </div>
      </div>
    </div>
    <!-- 主菜单 -->
    <div class="content">
      <div class="left">
        <div class="caption" :class="{ active: index == isActive }" v-for="(item,index) in content"
        :key="index" @click="changeLinks(index)">
          {{ item.name }}
        </div>
      </div>
      <div class="right" ref="rightscroll">
        <div class="warp">
          <div class="link" v-for="(item,index) in links" :key="index" @click="link(item.id)">
            {{ item.name }}
            <span class="nums">{{ item.nums }}</span>
          </div>
        </div>
      </div>
    </div>
    <!-- 底部菜单  -->
    <tabBar></tabBar>
  </div>
</template>

<script>
import tips from '@/components/Tips'
import tabBar from '@/components/TabBar'
import BScroll from 'better-scroll'
import { Search } from 'vux'
import wx from 'weixin-js-sdk'
export default {
  name: 'Treasury',
  components: {
    tips,
    Search,
    tabBar,
    BScroll
  },
  data () {
    return {
      content: [], // 所有产品的信息
      links: [], // 产品库跳转链接
      isActive: '', // 选中样式
      searchValue: '', // 搜索值
      results: [], // 搜索结果
      searchOver: false, // 搜索结果是否显示
      loading: false, // 是否显示正在加载
      end: false, // 是否搜索完
      noRes: false // 是否有搜索结果
    }
  },
  mounted () {
    this.getTreasury()
    this.$nextTick(() => {
      this.menuScroll()
      this.searchScroll()
      this.getWX()
    })
  },
  methods: {
    // 获取所有数据
    getTreasury () {
      this.axios.get('/api2/getSecurity').then(res => {
        this.content = res.data.content
        this.links = this.content[0].content
        this.isActive = 0
      })
    },
    // 搜索功能
    searching (value) {
      if (value === '') {
        this.searchOver = false
        this.end = false
        this.noRes = false
        return
      }
      this.axios.post('/api2/getSearchList', {
        keywords: value
      }).then(res => {
        this.searchOver = true
        this.results = res.data.content
      })
    },
    // 搜索框点击取消
    hide () {
      this.searchOver = false
      this.end = false
    },
    // 更换产品库内容
    changeLinks (index) {
      this.links = this.content[index].content
      this.isActive = index
    },
    // 跳转到产品详情
    toProduct (id) {
      this.$router.push({ name: 'ProductDetail', params: { id: id } })
    },
    // 搜索框scroll
    searchScroll () {
      this.scroll = new BScroll(this.$refs.loadmorescroll, {
        pullUpLoad: {
          threshold: -80
        },
        scrollY: true,
        mouseWheel: true,
        click: true,
        taps: true
      })
      this.scroll.on('pullingUp', () => {
        if (this.end) return
        this.loading = true
        this.axios.post('/api2/getSearchList', {
          keywords: this.searchValue,
          nums: this.results.length
        }).then(res => {
          if (res.data.content.length > 0) {
            setTimeout(() => {
              this.loading = false
              this.results = this.results.concat(res.data.content)
            }, 800)
          } else {
            setTimeout(() => {
              this.loading = false
              this.end = true
            }, 800)
          }
        }).then(() => {
          this.scroll.finishPullUp()
          this.scroll.refresh()
        })
      })
    },
    // 主菜单scroll
    menuScroll () {
      this.scroll2 = new BScroll(this.$refs.rightscroll, {
        scrollY: true,
        mouseWheel: true,
        click: true,
        taps: true
      })
    },
    link (id) {
      this.$router.push({ name: 'TreasuryList', params: {type: id} })
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
          title: 'AI保库 | 每个产品深度解析，覆盖全市场主流产品', // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: 'AI保库 | 每个产品深度解析，覆盖全市场主流产品', // 分享标题
          desc: '保险产品数据库，每个产品深度解析，覆盖全市场主流产品，让您任意查询。', // 分享描述
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
#treasury {
  height: 100%;
}

/* 搜索框 */
.loadmore-warp {
  position: fixed;
  background: #fff;
  height: calc(100% - 260px);
  width: 100%;
  z-index: 100;
  & .loadmore {
    height: 100%;
    overflow: hidden;
    & .warp {
      & .list {
        height: 100px;
        border-bottom: 1PX solid #e5e5e5;
        position: relative;
        display: flex;
        align-items: center;
        & span {
          width: 88%;
          font-size: var(--m-fs);
          margin-left: var(--edge);
        }
      }
      & .list:after {
        border: 4px solid #c8c8cd;
        border-bottom-width: 0;
        border-left-width: 0;
        content: " ";
        top: 50%;
        right: 40px;
        position: absolute;
        width: 10px;
        height: 10px;
        transform: translateY(-50%) rotate(45deg);
      }
    }
    & .end {
      height: 100px;
      line-height: 100px;
      text-align: center;
      color: var(--s-bgc);
      font-size: var(--m-fs);
    }
    & .loading {
      background: url("../../assets/img/loading.gif") no-repeat;
      background-size: 100%;
      display: block;
      width: 120px;
      height: 50px;
      margin: 40px auto;
    }
    & .noRes {
      text-align: center;
    }
  }
}

/* 主菜单 */
.content {
  display: flex;
  font-size: var(--m2-fs);
  width: 100%;
  height: calc(100% - 260px);
  & .left {
    flex: 1;
    background: #f5f5f5;
    color: var(--s-bgc);
    text-align: center;
    & .caption {
      position: relative;
      height: 100px;
      line-height: 100px;
      border-bottom: 1PX solid #e5e5e5;
      background: #fff;
    }
    & .active:before {
      position: absolute;
      content: "";
      width: 5px;
      height: 50%;
      left: 0;
      top: 25%;
      background: var(--blue);
    }
    & .active {
      background: #f5f5f5;
      color: var(--m-bgc);
    }
  }
  & .right {
    flex: 2;
    color: var(--m-bgc);
    height: 100%;
    border-left: 1PX solid #e5e5e5;
    overflow: hidden;
    & .link {
      height: 100px;
      line-height: 100px;
      background: #f5f5f5;
      border-bottom: 1PX solid #e5e5e5;
      text-indent: 30px;
    }
    & span {
      color: var(--s-bgc);
      float: right;
      margin-right: 30px;
    }
  }
}
</style>
