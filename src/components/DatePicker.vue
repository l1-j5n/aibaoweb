<template>
  <div id="datepicker">
    <div class="select" @click="showPicker">{{ selectedText[0] }}</div>
    <Picker @select="handleSelect(arguments)" @change="handleChange" :data="content" :selected-index="selectedIndex"
            ref="datepicker" :title="title" cancelTxt="取消" confirmTxt="确定"></Picker>
  </div>
</template>

<script>
import Picker from '@/components/Picker'
let year = (m, n) => {
  let year = []
  if (m > n) {
    var tmp = n
    n = m
    m = tmp
  }
  for (let i = m; i <= n; i++) {
    year.push({
      text: i + '年',
      value: i
    })
  }
  return year
}
let month = (m, n) => {
  let month = []
  if (m > n) {
    var tmp = n
    n = m
    m = tmp
  }
  for (let i = m; i <= n; i++) {
    month.push({
      text: i + '月',
      value: i
    })
  }
  return month
}
let day = (m, n) => {
  let day = []
  if (m > n) {
    var tmp = n
    n = m
    m = tmp
  }
  for (let i = m; i <= n; i++) {
    day.push({
      text: i + '日',
      value: i
    })
  }
  return day
}
let bigMonth = [1, 3, 5, 7, 8, 10, 12]
let smallMonth = [4, 6, 9, 11]
let leapYeay = year => {
  return (year % 4 === 0 && year % 100 !== 0) || year % 400 === 0
}
let date = new Date()
let today = [date.getFullYear(), date.getMonth() + 1, date.getDate()]
let yearsago = [date.getFullYear() - 80, date.getMonth() + 1, date.getDate()]
export default {
  name: 'DatePicker',
  components: {
    Picker
  },
  props: {
    text: {
      type: null,
      default () {
        return '请选择日期'
      }
    }, // 初始显示值
    title: {
      default () {
        return '出生日期'
      }
    },
    selected: {
      type: Array,
      default () {
        return [1990, 1, 1]
      }
    }, // 初始选项
    maxDate: {
      type: Array,
      default () {
        if (this.$route.name === 'Order') {
          return [2099, 1, 1]
        } else {
          return today
        }
      }
    },
    minDate: {
      type: Array,
      default () {
        if (this.$route.name === 'Order') {
          return [1900, 1, 1]
        } else {
          return yearsago
        }
      }
    },
    sign: {}
  },
  data () {
    return {
      yearList: year(this.minDate[0], this.maxDate[0]), // 年
      monthList: [], // 月
      dayList: [], // 日
      thisyear: 0, // 当前年
      thismonth: 0, // 当前月
      thisday: 0, // 当前日
      selectedIndex: [0, 0, 0], // 默认年月日
      selectedText: ['请选择时间'], // 外层显示
      isLeap: false
    }
  },
  mounted () {
    if (typeof this.text === 'object') {
      let tem = []
      tem = this.text[0] + '-' + this.text[1] + '-' + this.text[2]
      this.selectedText = [tem]
    } else {
      this.selectedText = [this.text]
    }
    this.thisyear = this.selected[0]
    this.thismonth = this.selected[1]
    this.thisday = this.selected[2]
    this.searchIndex(this.yearList, 0)
  },
  computed: {
    content () {
      return [this.yearList, this.monthList, this.dayList]
    }
  },
  methods: {
    showPicker () {
      this.$refs.datepicker.show()
    },
    handleSelect (args) {
      // 改变显示的值
      this.selectedText.splice(0, 1, args[0].join('-'))
      // 上传触发父组件更新
      let temData = []
      temData.push(this.thisyear, this.thismonth, this.thisday)
      this.$emit('update', [temData, this.sign])
    },
    handleChange (i, newIndex) {
      // i是列索引
      // newIndex是当前选项索引
      this.selectedIndex[i] = newIndex
      if (i === 1) {
        this.thismonth = this.content[i][newIndex].value
      } else if (i === 0) {
        this.thisyear = this.content[i][newIndex].value
      } else {
        this.thisday = this.content[i][newIndex].value
      }
    },
    // 用值找出索引并设置到picker上
    searchIndex (list, col) {
      for (let i = 0; i < list.length; i++) {
        if (list[i].value === this.selected[col]) {
          this.selectedIndex[col] = i
        }
      }
    },
    // 计算当前月有多少天
    calcDays (month) {
      if (bigMonth.indexOf(month) !== -1) {
        return 31
      } else if (smallMonth.indexOf(month) !== -1) {
        return 30
      } else {
        if (this.isLeap) {
          return 29
        } else {
          return 28
        }
      }
    }
  },
  watch: {
    thisyear (thisyear) {
      this.isLeap = leapYeay(this.thisyear)
      if (thisyear === this.maxDate[0] && thisyear === this.minDate[0]) {
        // 只有一年
        this.monthList = month(this.minDate[1], this.maxDate[1])
      } else if (thisyear === this.maxDate[0] && thisyear !== this.minDate[0]) {
        // 最大年
        this.monthList = month(1, this.maxDate[1])
        if (this.thismonth > this.maxDate[1]) {
          this.thismonth = this.maxDate[1]
        }
        if (thisyear === this.maxDate[0] && this.thismonth === this.maxDate[1]) {
          // 最大年最大月
          this.dayList = day(1, this.maxDate[2])
        } else {
          this.dayList = day(1, this.calcDays(this.thismonth))
        }
      } else if (thisyear === this.minDate[0] && thisyear !== this.maxDate[0]) {
        // 最小年
        this.monthList = month(this.minDate[1], 12)
        if (this.thismonth < this.minDate[1]) {
          this.thismonth = this.minDate[1]
        }
        if (this.thisyear === this.minDate[0] && this.thismonth === this.minDate[1]) {
          // 最小年最小月
          this.dayList = day(this.minDate[2], this.calcDays(this.thismonth))
        } else {
          this.dayList = day(1, this.calcDays(this.thismonth))
        }
      } else {
        // 普通年
        this.monthList = month(1, 12)
        this.dayList = day(1, this.calcDays(this.thismonth))
      }
    },
    thismonth (thismonth) {
      this.searchIndex(this.monthList, 1)
      this.isLeap = leapYeay(this.thisyear)
      if (this.maxDate[0] === this.minDate[0] && thismonth === this.minDate[1] && thismonth === this.maxDate[1]) {
        this.dayList = day(this.minDate[2], this.maxDate[2])
      } else if (this.thisyear === this.minDate[0] && thismonth === this.minDate[1]) {
        this.dayList = day(this.minDate[2], this.calcDays(thismonth))
      } else if (this.thisyear === this.maxDate[0] && thismonth === this.maxDate[1]) {
        this.dayList = day(1, this.maxDate[2])
      } else {
        this.dayList = day(1, this.calcDays(thismonth))
      }
    },
    thisday (thisday) {
      this.searchIndex(this.dayList, 2)
    }
  }
}
</script>

<style scoped>

</style>
