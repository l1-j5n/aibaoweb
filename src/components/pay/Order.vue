<template>
  <div id="order" v-title="'填写信息'">
    <alert v-model="showAlert" :title="'提示'" :content="alertContent"></alert>
    <loading :show="showLoading" text=""></loading>
    <div class="wrap" v-if="content[0]">
      <div class="title">投保人信息</div>
      <orderForm :content="content[0].inputs"></orderForm>
      <div v-if="parseInt(id) === 2574 || parseInt(id) === 2575 || parseInt(id) === 2577" class="bankinfo" style="text-align:left;padding:0 10px;font-size:13px;color:#666;">
        <p>1.中国税收居民：指在中国境内有住所，或者无住所而在境内居住满一年的人。</p>
        <p>2.非居民：指中国税收居民以外的个人。</p>
        <p>3.军人、武装警察持有有效的军官（武装警察）身份证件的即认为是中国税收居民。</p>
      </div>
    </div>
    <div class="wrap" v-if="content[1]">
      <div class="title">被保人信息</div>
      <orderForm :content="content[1].inputs"></orderForm>
    </div>
    <div class="wrap" v-if="content[2]">
      <div class="title">收益人信息</div>
      <orderForm :content="content[2].inputs"></orderForm>
    </div>
    <div class="wrap" v-if="content[3]">
      <div class="title">续期银行账户</div>
      <orderForm :content="content[3].inputs"></orderForm>
      <div class="bankinfo">
        <p>请使用<span>投保人本人</span>的银行卡借记卡</p>
        <div>
          <label>
            <input type="checkbox" v-model="bankinfo" id="tem">
            <label class="i-check" for="tem"></label>我已阅读并同意
            <a class="file-link" v-for="(file,key) in bankfile" :key="key" :href="file.src">{{ file.name }}</a>
          </label>
        </div>
      </div>
    </div>
    <div class="foot" v-if="lazy">
      <div class="btn" :class="{ sure: bankinfo }" @click="sub">下一步</div>
    </div>
  </div>
</template>

<script>
import { Alert, Loading } from 'vux'
import orderForm from '@/components/pay/OrderForm'
export default {
  name: 'Order',
  components: {
    orderForm,
    Alert,
    Loading
  },
  props: {
    id: {}, // 产品ID
    nums: {} // 份数
  },
  data () {
    return {
      content: [], // 总信息
      bankfile: [],
      bankinfo: false,
      lazy: false,
      showLoading: false,
      showAlert: false,
      alertContent: ''
    }
  },
  mounted () {
    this.getInfo()
  },
  methods: {
    // 获取所有信息
    getInfo () {
      this.axios.get('/api2/getOrderForm', {
        params: {
          plan_id: this.id
        }
      }).then(res => {
        this.content = res.data.keys
        // 增加税收居民身份
        if (parseInt(this.id) === 2574 || parseInt(this.id) === 2575 || parseInt(this.id) === 2577) {
          this.content[0].inputs.push({always_show: true, show: true, text: '税收居民身份', id: 'cc', field: {text: '请选择身份', type: 'lpysimplepicker', selected: [], data: [[{text: '中国税收居民', value: 0}, {text: '非居民', value: 1}]]}})
        }
        // 增加税收居民身份
        this.content[1].inputs.forEach((item, index, arr) => {
          if (item.id === 'recognizeeMul') {
            console.log(item)
            item.field.text = this.nums
            item.field.selected[0] = this.nums
          }
        })
        this.bankfile = res.data.bankfile
        if (!res.data.keys[3]) {
          this.bankinfo = true
        }
        // 不一定显示的项目增加字段
        this.content.forEach((item, index, arr) => {
          item.inputs.forEach((item, index, arr) => {
            if (!item.show) {
              item['temshow'] = false
              arr['temshow'] = item['temshow']
            }
          })
        })
      }).then(() => {
        this.lazy = true
      })
    },
    // 提交表单
    sub () {
      if (!this.bankinfo) return
      // 验证税收居民
      if (this.draw(0).cc && this.draw(0).cc[0] !== 0) {
        this.showAlert = true
        this.alertContent = '税收居民身份必须为中国税收居民'
        return
      }
      // 验证税收居民
      this.showLoading = true
      let tem = Object.assign(this.draw(0), this.draw(1), this.draw(2), this.draw(3))
      this.axios.post('/api2/genOrderSn', {
        id: this.id,
        info: JSON.parse(localStorage.tryInfo),
        params: tem
      }).then(res => {
        this.showLoading = false
        if (res.data.code !== 0) {
          this.showAlert = true
          this.alertContent = res.data.msg
        } else {
          this.$router.push({ name: 'OrderConfirm', params: { orderSn: res.data.orderSn } })
        }
      })
    },
    // 组合数据
    draw (index) {
      if (index > this.content.length - 1) return
      let tem = {}
      this.content[index].inputs.forEach((item, idx, arr) => {
        if (index === 1 && arr[0].field.selected[0] === '0' && !item.show) return
        if (index === 2 && arr[0].field.selected[0] === '00' && !item.show) return
        if (item.field.type === 'lpypicker' || item.field.type === 'lpydatepicker' || item.field.type === 'lpysimplepicker' || item.field.type === 'time') {
          tem[item.id] = item.field.selected
        } else {
          tem[item.id] = item.value
        }
      })
      return tem
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
.wrap {
  & .title {
    font-size: var(--price-fs);
    color: var(--d-bgc);
    padding: var(--edge) calc(2 * var(--edge));
    position: relative;
    height: 80px;
    line-height: 80px;
    border-bottom: 1px solid #e5e5e5;
  }
  & .title:before {
    content: "";
    position: absolute;
    left: var(--edge);
    top: 40px;
    width: 10px;
    height: 40px;
    background: var(--blue);
  }
  & input {
    color: var(--m-bgc);
  }
  & .bankinfo {
    text-align: center;
    color: var(--b-bgc);
    font-size: var(--m-fs);
    background: #fff;
    padding: var(--edge) 0;
    border-bottom: 2PX solid #e5e5e5;
    & p {
      padding-bottom: var(--edge);
    }
    & span {
      color: var(--red);
    }
    .file-link {
      line-height: 30px;
    }
    & input {
      display: none;
    }
    & .i-check {
      display: inline-block;
      width: 40px;
      height: 40px;
      border: 2PX solid #eee;
      border-radius: 50%;
      background-size: 100%;
      margin: 0 10px -10px 0;
    }
    & input[type=checkbox]:checked + .i-check{
      background: var(--blue) url('../../assets/img/check.png') center center no-repeat;
      background-size: 50% 40%;
      width: 40px;
      height: 40px;
      border-radius: 50%;
      top: 110px;
      right: 0;
    }
  }
}

/* 确定按钮 */
.foot {
  padding: 40px 0;
  & .btn {
    text-align: center;
    font-size: var(--m-fs);
    color: #fff;
    background-color: #ccc;
    box-shadow: 0 0.111rem 0.417rem #eee;
    width: 80%;
    height: 80px;
    line-height: 80px;
    margin: 0 auto;
    border-radius: 10px;
  }
  & .sure {
    background-color: var(--blue);
    box-shadow: 0 0.111rem 0.417rem #c1dcf5;
  }
}
</style>
