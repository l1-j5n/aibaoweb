<template>
  <div id="app">
    <keep-alive>
      <router-view v-if="$route.meta.keepAlive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive"></router-view>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
@import '/assets/css/global.css';
* {
  margin: 0;
  padding: 0;
}

html,body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
             "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB",
             "Microsoft YaHei",SimSun, sans-serif;
  background: #f8f8f8;
  line-height: 1.5;
  height: 100%;
  width: 100%;
}

#app {
  height: 100%;
  width: 100%;
}

/* svg */
.icon {
  width: 2em;
  height: 2em;
  vertical-align: middle;
  fill: currentColor;
  overflow: hidden;
  margin-top: -7px;
}

/* 相关文件弹出层的样式 */
.weui-actionsheet__menu .vux-actionsheet-menu-default {
  height: 60px;
  line-height: 60px;
  color: #69c;
  font-size: 32px;
}

/* 保库搜索框 */
.weui-search-bar {
  height: 90px;
  line-height: 45px;
  background-color: #69c !important;
  padding: 18px !important;
  & .weui-search-bar__label {
    text-align: left;
    & span {
      margin-left: -2px;
      font-size: 34px;
      color: #666;
    }
  }
  & .weui-icon-search {
    margin-left: 12px;
    margin-top: 3px;
    font-size: 26px;
    color: #d9d9d9;
  }
  & .weui-search-bar__box .weui-icon-search::before {
    margin-top: 12px;
    margin-left: -4px;
  }
  & .weui-search-bar__input {
    font-size: 34px !important;
    margin-left: 30px;
    margin-top: -2px;
  }
  & .weui-icon-clear {
    margin-top: 15px;
    font-size: 30px;
  }
  & .weui-search-bar__cancel-btn {
    color: #fff;
    text-decoration: none;
    line-height: 50px;
    font-size: 32px;
  }
}

/* 遮罩层 */
#app .weui-mask, .weui-mask_transparent {
  z-index: 99;
}

#app #contrast .weui-mask, .weui-mask_transparent {
  z-index: 100;
}

/* confirm插件的样式 */
#app .weui-dialog {
  border-radius: 10px;
  width: 85%;
  max-width: none;
  & .weui-dialog__title {
    color: var(--d-bgc);
    font-size: var(--b-fs);
    font-weight: bold;
  }
  & .weui-dialog__bd {
    color: var(--d-bgc);
    font-size: var(--m-fs);
    line-height: 2;
  }
  & .weui-dialog__ft {
    height: 80px;
    line-height: 80px;
    font-size: var(--b-fs);
    color: var(--d-bgc);
    & .weui-dialog__btn_primary {
      color: #26a2ff;
    }
  }
}

#app #product-detail .vux-actionsheet-menu-default {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
}

#app #decisioncase {
  & .vux-prompt-msgbox {
    border: 1PX solid #dedede;
    height: 40px;
    font-size: var(--s-fs);
  }
}

#app #decisionstep .vux-loading .weui-toast {
  margin-top: 50%;
  min-height: 140px;
  width: 200px;
  & i {
    width: 80px;
    height: 80px;
  }
}

#app #product-detail .vux-loading,
#app #treasurylist .vux-loading {
  z-index: 9999;
  position: flex;
}

#app #product-detail .vux-loading .weui-toast,
#app #treasurylist .vux-loading .weui-toast {
  margin-top: 55%;
  min-height: 140px;
  width: 200px;
  & i {
    width: 80px;
    height: 80px;
  }
}

/* toast */
#app .weui-toast {
  height: 80px;
  line-height: 60px;
  & .weui-toast__content {
    font-size: var(--b-fs);
  }
}

/* alert */
#app .vux-alert .weui-dialog {
  & .weui-dialog__ft {
    color: #26a2ff;
  }
}

/* loading */
#app #order .weui-toast {
  top: 50%;
  height: 130px;
  & .weui-icon_toast {
    width: 70px;
    height: 70px;
  }
  & .weui-toast__content {
    margin-top: -20px;
  }
}
</style>
