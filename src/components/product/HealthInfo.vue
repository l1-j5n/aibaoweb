<template>
  <div id="healthinfo" v-title="'健康告知'">
    <div class="title">健康告知</div>
    <div class="main">
      <div>{{ txt[0] }}</div>
      <div>{{ txt[1] }}</div>
      <div>{{ txt[2] }}</div>
      <div>{{ txt[3] }}</div>
    </div>
    <div class="state">
      <div>免责声明</div>
      <div>本投保人声明上述内容填写属实，如有隐瞒或告知不实，保险公司有权依据《中华人民共和国保险法》
        解除保险合同，对于合同解除前发生的任何事故，保险公司可不承担任何责任。</div>
    </div>
    <confirm v-model="show" title="提示" @on-confirm="onConfirm">
      <p style="text-align:center;">不符合健康要求，无法购买此产品！<br/>点击确定返回产品详情页</p>
    </confirm>
    <div class="foot">
      <div class="left" @click="goback">部分情况有</div>
      <div class="right" @click="goon">以上情况全无</div>
    </div>
  </div>
</template>

<script>
import { Confirm } from 'vux'
export default {
  name: 'HealthInfo',
  components: {
    Confirm
  },
  props: {
    id: {}
  },
  data () {
    return {
      show: false,
      txt: []
    }
  },
  mounted () {
    this.axios.get('/api2/healthyInfo', {
      params: {
        plan_id: this.id
      }
    }).then(res => {
      this.txt = res.data.healthyInfo
    })
  },
  methods: {
    goback () {
      this.show = true
    },
    goon () {
      this.$router.push({ name: 'Order', params: { id: this.id } })
    },
    onConfirm () {
      this.$router.go(-1)
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
#healthinfo {
  height: calc(100% + 260px);
  padding: 40px 20px;
}

.title {
  text-align: center;
  font-size: var(--b-fs);
  font-weight: bold;
}

.main {
  padding: var(--edge);
  color: var(--m-bgc);
  font-size: var(--s-fs);
  & div {
    margin-bottom: 40px;
  }
}

.state {
  padding: var(--edge);
  background: #f0f5fa;
  color: var(--blue);
  & div:nth-child(1) {
    font-size: var(--m-fs);
    padding-bottom: 20px;
  }
  & div:nth-child(2) {
    font-size: var(--s-fs);
  }
}

.foot {
  position: fixed;
  bottom: 0;
  left: 0;
  height: 100px;
  line-height: 100px;
  width: 100%;
  display: flex;
  text-align: center;
  font-size: var(--m-fs);
  & .left {
    flex: 2;
    background: #eee;
    color: var(--m-bgc);
  }
  & .right {
    flex: 3;
    background: var(--blue);
    color: #fff;
  }
}
</style>
