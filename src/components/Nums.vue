<template>
  <div id="nums">
    <div class="left" @click="reduce">-</div>
    <div class="main">{{ num }}</div>
    <div class="right" @click="add">+</div>
  </div>
</template>

<script>
export default {
  name: 'Nums',
  props: {
    selected: {},
    max: {},
    min: {},
    sign: {}
  },
  data () {
    return {
      num: ''
    }
  },
  mounted () {
    this.num = this.selected
  },
  methods: {
    reduce () {
      if (this.num > this.min) {
        this.num = this.num - 1
        this.$emit('update', [this.num, this.sign])
      }
    },
    add () {
      if (this.num < this.max) {
        this.num = this.num + 1
        this.$emit('update', [this.num, this.sign])
      }
    }
  }
}
</script>

<style scoped>
@import '../assets/css/global.css';
#nums {
  display: flex;
  justify-content: space-between;
  color: var(--m-bgc);
  border: 1PX solid #e5e5e5;
  border-radius: 10px;
}

.left,.right {
  flex: 2;
}

.main {
  flex: 3;
  border-left: 1PX solid #e5e5e5;
  border-right: 1PX solid #e5e5e5;
}
</style>
