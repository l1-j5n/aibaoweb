<template>
  <div id="top-bar">
    <!-- 标题 -->
    <div class="main">
      <div class="left" @click="back">
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-moreDown"></use>
        </svg> 返回
      </div>
      <div class="title">{{ title }}</div>
      <div class="right" @click="changeMenu">
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-gengduo"></use>
        </svg>
      </div>
    </div>
    <!-- 弹出菜单 -->
    <div class="menu" v-show="showMenu">
      <div v-for="item in menus" :key="item.index" @click="link(item.index)">{{ item.name }}</div>
    </div>
    <!-- 遮罩 -->
    <div class="mask" @click="changeMenu" v-show="showMenu"></div>
  </div>
</template>

<script>
export default {
  name: 'TopBar',
  props: {
    title: {}
  },
  data () {
    return {
      showMenu: false,
      menus: [
        {name: '严选', index: 'Strict'},
        {name: '保库', index: 'Treasury'},
        {name: '比价', index: 'Contrast'},
        {name: '定制', index: 'Decision'},
        {name: '我的', index: 'User'}
      ]
    }
  },
  methods: {
    back () {
      this.$router.go(-1)
    },
    changeMenu () {
      this.showMenu = !this.showMenu
    },
    // 切换主页面
    link (path) {
      this.$router.push({ name: path })
    }
  }
}
</script>

<style scoped>
@import '../assets/css/global.css';
.main {
  display: flex;
  text-align: center;
  height: 90px;
  line-height: 90px;
  color: var(--m-bgc);
  border-bottom: 1px solid #eee;
  background: var(--bgc);
}

.left {
  flex: 1;
  font-size: var(--m-fs);
  & .icon {
    transform: rotate(90deg);
    width: 30px;
    height: 32px;
  }
}

.title {
  flex: 4;
  color: var(--blue);
  font-size: var(--b-fs);
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
}

.right {
  flex: 1;
}

/* 弹出的菜单 */
.menu {
  position: absolute;
  background: var(--bgc);
  width: 100%;
  z-index: 100;
  text-align: center;
  font-size: var(--b-fs);
  color: var(--m-bgc);
  & div {
    height: 100px;
    line-height: 100px;
  }
}

/* 遮罩 */
.mask {
  position: fixed;
  z-index: 99;
  opacity: .7;
  background: #000;
  height: 100%;
  width: 100%;
}
</style>
