<template>
  <div id="user" v-title="'用户中心'">
    <!-- banner图 -->
    <div class="banner">
      <img :src="content.img" alt="">
      <div class="name">{{ content.name }}</div>
    </div>
    <!-- 保单 -->
    <div class="warp" @click="link('/userorder/1')">
      <div class="left">
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-form"></use>
        </svg>
        我的保单
      </div>
      <div class="right">></div>
    </div>
    <div class="wrap-order">
      <div class="order-link" v-for="(item,index) in orderList" :key="index" @click="link(item.src)">
        <svg class="icon" aria-hidden="true" :style="'color:'+item.color">
          <use :xlink:href="item.icon"></use>
        </svg>
        <div class="text">{{ item.name }}</div>
      </div>
    </div>
    <!-- 其余4个链接 -->
    <div class="warp" v-for="(item,index) in menus" :key="index" @click="link(item.src)">
      <div class="left">
        <svg class="icon" aria-hidden="true">
          <use :xlink:href="item.icon"></use>
        </svg>
        {{ item.name }}
      </div>
      <div class="right">></div>
    </div>
    <!-- 底部菜单 -->
    <tabBar></tabBar>
  </div>
</template>

<script>
import tabBar from '@/components/TabBar'
export default {
  name: 'User',
  components: {
    tabBar
  },
  data () {
    return {
      menus: [
        {
          name: '常见问题',
          icon: '#icon-remind',
          src: 'UserQuestion'
        },
        {
          name: '理赔指南',
          icon: '#icon-compass',
          src: 'UserClaims'
        },
        {
          name: '关于我们',
          icon: '#icon-account',
          src: 'AboutUs'
        },
        {
          name: '报备产品',
          icon: '#icon-WENDANG',
          src: 'https://cdn.aibaodata.com/%E4%BA%A7%E5%93%81%E4%BF%A1%E6%81%AF%E6%8A%A5%E9%80%81%E7%9B%AE%E5%BD%95.pdf'
        }
      ],
      orderList: [
        {
          name: '待支付',
          icon: '#icon-daizhifu',
          src: '/userorder/2',
          color: '#f90'
        },
        {
          name: '已支付',
          icon: '#icon-success',
          src: '/userorder/3',
          color: '#0cc277'
        },
        {
          name: '保障中',
          icon: '#icon-security',
          src: '/userorder/4',
          color: '#6b9cce'
        }
      ],
      content: []
    }
  },
  mounted () {
    this.getInfo()
  },
  methods: {
    // 获取个人信息
    getInfo () {
      this.axios.get('/api2/getUser').then(res => {
        this.content = res.data.content
      })
    },
    // 各详情页跳转
    link (src) {
      if (src.indexOf('https') === -1) {
        this.$router.push({ path: src })
      } else {
        window.location.href = src
      }
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
#user {
  padding-bottom: 20px;
}

/* banner图 */
.banner {
  height: 300px;
  background-size: cover;
  background-image: url('../../assets/img/user-banner.jpg');
  background-position: center center;
  overflow: hidden;
  & img {
    display: block;
    width: 160px;
    height: 160px;
    margin: 50px auto 0;
    border-radius: 50%;
  }
  & .name {
    text-align: center;
    color: #fff;
    font-size: var(--m-fs);
  }
}

/* 链接标题 */
.warp {
  display: flex;
  height: 100px;
  line-height: 100px;
  border-bottom: 1px solid #e5e5e5;
  padding: 0 calc(2 * var(--edge));
  font-size: var(--m-fs);
  color: var(--m-bgc);
  background: var(--bgc);
  & .left {
    flex: 4;
  }
  & .right {
    flex: 1;
    text-align: right;
    color: #a2a2a2;
  }
  & .icon {
    width: 30px;
    height: 30px;
  }
}

/* 快捷保单样式 */
.wrap-order {
  display: flex;
  padding: var(--edge) 0;
  border-bottom: 1px solid #e5e5e5;
  background: #fff;
  & .order-link {
    flex: 1;
    text-align: center;
    padding: 20px 0;
  }
  & .icon {
    width: 60px;
    height: 60px;
  }
  & .text {
    margin-top: 10px;
    font-size: var(--s-fs);
    color: var(--d-bgc);
  }
}
</style>
