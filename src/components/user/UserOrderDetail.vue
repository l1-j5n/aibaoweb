<template>
  <div id="userorderdetail" v-title="'我的保单'">
    <topBar :title="'保单详情'"></topBar>

    <div class="list">
      <div class="caption">
        {{ product.title }}
        <div v-if="product.type==20||product.type==21" :class="['type','type1']"> 保障中 </div>
        <div v-else-if="product.type==30" :class="['type','type2']"> 已过期 </div>
        <div v-else-if="product.type==10" :class="['type','type3']"> 已支付 </div>
        <div v-else-if="product.type==2" :class="['type','type4']"> 待支付 </div>
        <div v-else-if="product.type==12" :class="['type','type2']"> 已关闭 </div>
      </div>
      <div class="info">
        <div>
          <div>保单号：{{ product.number }}</div>
          <div>生效日期：{{product.dateBegin}}</div>
          <div v-if="product.dateEnd!=''">终止日期：{{product.dateEnd}}</div>
          <div v-if="product.wait!=''">等待期：{{product.wait}}</div>
          <div>保障额度：{{product.quota}}</div>
          <div>支付金额：{{product.price}}元</div>
          <div>支付时间：{{product.payTime}}</div>
        </div>
        <img :src="product.logo" alt="" class="logo">
      </div>
    </div>

    <div class="insuredlist" v-if="insured">
      <div class="caption">被保人信息：</div>
      <div class="info">
        <div>姓名：{{ insured.name }}</div>
        <div>证件类型：{{ insured.type }}</div>
        <div>证件号：{{ insured.number }}</div>
        <div>手机号：{{ insured.phone }}</div>
      </div>
    </div>

    <div class="policyholderlist" v-if="policyholder">
      <div class="caption">投保人信息：</div>
      <div class="info">
        <div>姓名：{{ policyholder.name }}</div>
        <div>证件类型：{{ policyholder.type }}</div>
        <div>证件号：{{ policyholder.number }}</div>
        <div>手机号：{{ policyholder.phone }}</div>
      </div>
    </div>

    <div class="foot" v-if="type==2">
      <div class="submit" @click="pay">继续支付</div>
      <div class="unsubmit" @click="dele">删除订单</div>
    </div>
    <div class="foot" v-if="type==12">
      <div class="submit" @click="buy">继续购买</div>
      <div class="unsubmit" @click="dele">删除订单</div>
    </div>
    <div class="foot" v-if="type==20||type==21||type==20||type==10||type==30">
      <div class="submit" @click="buy">再次购买</div>
    </div>
    </div>
</template>

<script>
import topBar from '@/components/TopBar'
export default {
  name: 'UserOrderDetail',
  components: {
    topBar
  },
  props: {
    id: {}
  },
  data () {
    return {
      product: [], // 产品信息
      insured: [], // 被保人
      policyholder: [], // 投保人
      type: ''
    }
  },
  mounted () {
    this.getInfo()
  },
  methods: {
    getInfo () {
      this.axios.get('/api2/getOrderDetail', {
        params: {
          id: this.id
        }
      }).then(res => {
        this.product = res.data.content.product
        this.insured = res.data.content.insured
        this.policyholder = res.data.content.policyholder
        this.type = this.product.type
      })
    },
    pay () {
      this.$router.push({name: 'PaySub', params: {orderSn: this.product.orderSn}})
    },
    dele () {
      this.axios.get('/api2/cancelOrder', {
        params: {
          orderSn: this.product.orderSn
        }
      }).then(res => {
        if (res.data.code === 0) {
          this.$router.go(-1)
        }
      })
    },
    buy () {
      this.$router.push({name: 'ProductDetail', params: {id: this.product.planid}})
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
.list {
  background: #fff;
  padding: 40px;
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
      align-self: flex-end;
    }
  }
  & .info > div:nth-child(1) div {
    padding-bottom: 10px;
  }
}

.insuredlist,
.policyholderlist {
  padding: 40px;
  & .caption {
    font-size: var(--m2-fs);
    margin-bottom: 40px;
  }
  & .info {
    font-size: var(--s-fs);
    color: var(--m-bgc);
  }
  & .info div {
    padding-bottom: 10px;
  }
}

.insuredlist {
  border-bottom: 2PX solid #fff;
}

.foot {
  padding: 40px 0;
}

.foot > div:nth-child(2) {
  margin-top: 40px;
}

.submit,
.unsubmit {
  text-align: center;
  font-size: var(--m-fs);
  color: #fff;
  background-color: var(--blue);
  box-shadow: 0 0.111rem 0.417rem #c1dcf5;
  width: 80%;
  height: 80px;
  line-height: 80px;
  margin: 0 auto;
  border-radius: 10px;
}

.unsubmit {
  background: var(--red);
}
</style>
