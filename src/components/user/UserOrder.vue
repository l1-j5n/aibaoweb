<template>
  <div id="userorder" v-title="'我的保单'">
    <topBar :title="'我的保单'"></topBar>

    <div class="tab">
      <div :class="{ active: index + 1 == type }" v-for="(item,index) in tabMenu" :key="index" @click="changeType(index)">{{ item }}</div>
    </div>

    <div class="list" v-for="(item,index) in content" :key="index" @click="link(item.id)">
      <div class="caption">
        {{ item.title }}
        <div v-if="item.type==20||item.type==21" :class="['type','type1']"> 保障中 </div>
        <div v-else-if="item.type==30" :class="['type','type2']"> 已过期 </div>
        <div v-else-if="item.type==10" :class="['type','type3']"> 已支付 </div>
        <div v-else-if="item.type==2" :class="['type','type4']"> 待支付 </div>
        <div v-else-if="item.type==12" :class="['type','type2']"> 已关闭 </div>
      </div>
      <div class="info">
        <div>
          <div>被保人：{{ item.name }}</div>
          <div>保险费：{{ item.price }}</div>
          <div>支付期：{{ item.date }}</div>
        </div>
        <img :src="item.logo" alt="" class="logo">
      </div>
    </div>
  </div>
</template>

<script>
import topBar from '@/components/TopBar'
export default {
  name: 'UserOrder',
  components: {
    topBar
  },
  props: {
    type: {}
  },
  data () {
    return {
      tabMenu: ['全部', '待支付', '已支付', '保障中'],
      content: []
    }
  },
  mounted () {
    this.getInfo()
  },
  methods: {
    getInfo () {
      this.axios.get('/api2/getOrder', {
        params: {
          type: this.type
        }
      }).then(res => {
        this.content = res.data.content
      })
    },
    changeType (index) {
      this.$router.push({ name: 'UserOrder', params: {type: index + 1} })
    },
    link (id) {
      this.$router.push({ name: 'UserOrderDetail', params: {id: id} })
    }
  },
  watch: {
    $route () {
      this.getInfo()
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
.tab {
  display: flex;
  background: #fff;
  height: 80px;
  line-height: 80px;
  text-align: center;
  font-size: var(--m3-fs);
  color: var(--d-bgc);
  border-bottom: 1PX solid #eee;
  & div {
    flex: 1;
  }
  & div:not(:last-child) {
    border-right: 1PX solid #eee;
  }
  & .active {
    color: var(--blue);
    position: relative;
  }
  & .active:after {
    content: "";
    position: absolute;
    width: 50%;
    height: 4px;
    left: 25%;
    bottom: -1px;
    background: var(--blue);
  }
}

.list {
  background: #fff;
  margin-top: 20px;
  padding: 40px;
  height: 220px;
  & .caption {
    display: flex;
    justify-content: space-between;
    font-size: var(--m2-fs);
    margin-bottom: 40px;
    & .type {
      color: #fff;
      font-size: var(--m3-fs);
      width: 140px;
      text-align: center;
    }
    & .type1 {
      background: var(--blue);
    }
    & .type2 {
      background: #ccc;
    }
    & .type3 {
      background: #63d2ad;
    }
    & .type4 {
      background: #ff9600;
    }
  }
  & .info {
    display: flex;
    justify-content: space-between;
    font-size: var(--s-fs);
    color: var(--m-bgc);
    & img {
      width: 150px;
      height: 150px;
    }
  }
  & .info > div:nth-child(1) div {
    padding-bottom: 10px;
  }
}
</style>
