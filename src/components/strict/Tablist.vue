<template>
  <div id="tab-list">
    <div class="list" v-for="(item,index) in content" :key="index" @click="link(item.id)">
      <div class="main">
        <div class="pic">
          <img :src="item.pic" alt="">
        </div>
        <div class="info">
          <div class="name">{{ item.name }}</div>
          <div class="label">{{ item.labels }}</div>
          <div class="price">
            <span>￥{{ item.price }}</span>&nbsp;起
          </div>
        </div>
      </div>
      <div class="foot">
        "{{ item.reason }}"
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Tablist',
  props: {
    content: {} // 产品列表
  },
  methods: {
    // 跳转到产品详情页
    link (id) {
      this.$router.push({ name: 'ProductDetail', params: { id: id, formIndex: true } })
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
#tab-list {
  height: 100%;
}

.list {
  margin-top: 20px;
  background: #fff;
  padding: var(--edge);
  & .pic {
    float: left;
    width: 218px;
    height: 218px;
    & img {
      width: 100%;
      height: 100%;
    }
  }
  & .info {
    overflow: hidden;
    padding-left: var(--edge);
    & .name {
      font-size: var(--b-fs);
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
  & .foot {
    clear: both;
    position: relative;
    margin-top: 30px;
    padding: var(--edge);
    font-size: var(--s-fs);
    color: var(--m-bgc);
    background: #f0f5fa;
    &:after {
      position: absolute;
      content: "";
      left: 30px;
      top: -30px;
      border-style: dashed dashed solid dashed;
      border-color: transparent transparent #f0f5fa transparent;
      border-width: 15px;
    }
  }
}

.list:nth-last-child(1) {
  margin-bottom: 120px;
}
</style>
