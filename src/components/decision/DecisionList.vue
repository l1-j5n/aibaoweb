<template>
  <div id="decisionlist" v-title="'定制'">
    <topBar :title="'已保存的方案'"></topBar>

    <confirm v-model="confirmShow" :title="'提示'" :content="'确认删除？'" @on-confirm="onConfirm"></confirm>

    <div class="list" v-for="(item,index) in content" :key="index">
      <div class="scrollbox">
        <div class="caption" @click="link(item.id)">
          <img :src="item.img" alt="">
          <div class="name">{{ item.name }}</div>
          <div class="date">{{ item.date }}</div>
        </div>
        <div class="price" @click="link(item.id)">保费预算：<span>￥{{ item.price }}</span></div>
        <div class="del" @click="confirmShow = true, id = item.id">删除</div>
      </div>
    </div>
  </div>
</template>

<script>
import topBar from '@/components/TopBar'
import { Confirm } from 'vux'
export default {
  name: 'DecisionList',
  components: {
    topBar,
    Confirm
  },
  data () {
    return {
      content: [],
      confirmShow: false,
      id: ''
    }
  },
  mounted () {
    this.getInfo()
  },
  methods: {
    getInfo () {
      this.axios.get('/api2/Decision/getDecisionList').then(res => {
        this.content = res.data.content
      })
    },
    link (id) {
      this.$router.push({ name: 'DecisionCase', params: {id: id} })
    },
    onConfirm () {
      this.axios.post('/api2/Decision/unSubResult', {
        id: this.id
      }).then((res) => {
        this.getInfo()
      })
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
#decisionlist {
  width: 100%;
}

.list {
  overflow-x: scroll;
  overflow-y: hidden;
}

.scrollbox {
  display: flex;
  justify-content: space-between;
  background: #fff;
  height: 120px;
  width: 110%;
  margin-bottom: 20px;
  & .caption {
    width: 50%;
  }
  & img {
    width: 100px;
    height: 100px;
    border: 1PX solid var(--blue);
    border-radius: 50%;
    float: left;
    margin: 10px 20px;
  }
  & .name {
    font-size: var(--m3-fs);
    float: left;
    margin-top: 20px;
    width: 50%;
  }
  & .date {
    color: var(--s-bgc);
    font-size: var(--s-fs);
    float: left;
  }
  & .price {
    line-height: 120px;
    margin-right: 10px;
  }
  & .del {
    color: #fff;
    font-size: var(--m3-fs);
    background: red;
    text-align: center;
    line-height: 120px;
    width: 80px;
  }
}
</style>
