<template>
  <div id="orderform">
    <div class="main" v-for="(item,index) in content" :key="index" v-if="item.temshow || item.show">
      <!-- 文本类型 -->
      <div class="row" v-if="item.field.type == 'text'">
        <div class="left">{{ item.text }}</div>
        <div class="right">
          <input type="text" v-model="item.value" placeholder="请输入" :disabled="item.field.isReadOnly">
        </div>
      </div>
      <!-- 数字 -->
      <div class="row" v-if="item.field.type == 'number'">
        <div class="left">{{ item.text }}</div>
        <div class="right">
          <input type="number" v-model="item.value" placeholder="请输入">
        </div>
      </div>
      <!-- picker -->
      <div class="row" v-if="item.field.type == 'lpysimplepicker'">
        <div class="left">{{ item.text }}</div>
        <div class="right">
          <allPicker @update="updateSingle($event, index)" :content="item.field.data" :text="item.field.text" :selected="item.field.selected[0]" :title="item.text"></allPicker>
        </div>
      </div>

      <div class="row" v-if="item.field.type == 'lpypicker'">
        <div class="left">{{ item.text }}</div>
        <div class="right">
          <allPicker @update="updateLink($event, index)" :url="item.field.file" :title="item.text"></allPicker>
        </div>
      </div>
      <!-- 日期picker -->
      <div class="row" v-if="item.field.type == 'lpydatepicker'">
        <div class="left">{{ item.text }}</div>
        <div class="right">
          <datePicker @update="updatedate($event, index)" :text="item.field.text" :selected="item.field.selected" :maxDate="item.field.maxDate" :minDate="item.field.minDate" :title="item.text"></datePicker>
        </div>
      </div>
      <!-- 时间 -->
      <div class="row" v-if="item.field.type == 'time'">
        <div class="left">{{ item.text }}</div>
        <div class="right">
          <timePicker @update="updateTime($event, index)"></timePicker>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import datePicker from '@/components/DatePicker'
import allPicker from '@/components/AllPicker'
import timePicker from '@/components/TimePicker'
export default {
  name: 'OrderForm',
  components: {
    datePicker,
    allPicker,
    timePicker
  },
  props: {
    content: {}
  },
  data () {
    return {}
  },
  mounted () {
  },
  methods: {
    updateSingle (value, index) {
      this.content[index].field.selected[0] = value[0]
      // 切换渲染多余数据
      if (this.content[index].id === 'recognizeeAppntRelation' && this.content[index].field.selected[0] !== '0') {
        this.cut(true)
      } else if (this.content[index].id === 'recognizeeAppntRelation' && this.content[index].field.selected[0] === '0') {
        this.cut(false)
      }
      if (this.content[index].id === 'relationToInsured' && this.content[index].field.selected[0] !== '00') {
        this.cut(true)
      } else if (this.content[index].id === 'relationToInsured' && this.content[index].field.selected[0] === '00') {
        this.cut(false)
      }
    },
    updateLink (value, index) {
      this.content[index].field.selected = value
    },
    updatedate (value, index) {
      this.content[index].field.selected = value[0]
    },
    updateTime (value, index) {
      let tem = ''
      tem = value[0] + ':' + value[1]
      this.content[index].field.selected = tem
    },
    // 切换渲染多余数据
    cut (state) {
      this.content.forEach((item, index, arr) => {
        if (!item.show) {
          item.temshow = state
        }
      })
      this.content.push({})
      this.content.pop()
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
.row {
  padding: 0 var(--edge);
  min-height: 100px;
  line-height: 100px;
  background: #fff;
  border-bottom: 2PX solid #eee;
  display: flex;
  font-size: var(--m-fs);
  & .left {
    flex: 1;
  }
  & .right {
    flex: 2;
    text-align: right;
    color: var(--m-bgc);
  }
  & input {
    text-align: right;
    font-size: var(--m-fs);
    border: none;
    background-color: #fff;
  }
  & input:focus {
    outline: none;
  }
}
</style>
