<template>
  <div id="aboutus" v-title="'关于我们'">
    <topBar :title="'关于我们'"></topBar>
<!-- 头部菜单 -->
    <div class="menu">
      <div v-for="(item,index) in menus" :key="index" :class="{ active: isactive ===index }" @click="changeMenu(index)">{{ item }}</div>
    </div>
    <!-- 主要内容 -->
    <!-- 关于我们 -->
    <div class="one" v-if="isactive == 0">
      <div class="text1">
        <div class="banner">
          <img src="../../assets/img/aboutUs-banner1.jpg" alt="">
        </div>
        <div class="text">
          我们是一家专注保险科技的企业，致力于用保险科技的创新能力和专业能力服务广大用户。
          我们的团队是一群热爱保险事业的人：拥有丰富从业经验的保险精算师，IT工程师、
          大数据算法工程师、金融管理咨询专家，以及热情并充满活力的互联网运营和客户服务人员。
        </div>
      </div>
      <div class="text2">
        <div class="title">我们的价值主张</div>
        <div class="text">
          捍卫家庭幸福，笑对人生疾苦。好的保障，没那么贵。通过我们的智能选保工具和AI保严选平台，
          让用户从全市场选择到最适合自己且最具性价比的保险产品。健康险等用户刚性需求的产品，
          会是我们关注的重点。我们帮助用户首先满足保险的基础刚性需求。
        </div>
        <div class="banner">
          <img src="../../assets/img/aboutUs-banner2.jpg" alt="">
        </div>
      </div>
      <div class="text3">
        <div class="title">我们的优势</div>
        <div v-for="(item,index) in goodInfo" :key="index" class="goodness">
          <div class="left">
            <div class="icon" :class="'icon' + index"></div>
            <div>{{ item.title }}</div>
          </div>
          <div class="right">{{ item.text }}</div>
        </div>
      </div>
    </div>
    <!-- 管理团队 -->
    <div class="two" v-if="isactive == 1">
      <div v-for="(item,index) in teamInfo" :key="index" class="list">
        <div class="left">
          <img :src="item.photo" alt="">
        </div>
        <div class="right">
          <div class="name">{{ item.title }}</div>
          <div class="label">{{ item.info }}</div>
        </div>
      </div>
    </div>
    <!-- 联系我们 -->
    <div class="three" v-if="isactive == 2">
      <div v-for="(item,index) in contact" :key="index" class="info">
        <div class="ico">
          <svg class="icon" aria-hidden="true">
            <use :xlink:href="item.ico"></use>
          </svg>
        </div>
        <div class="text" :class="{ blue: index === 2 }">{{ item.txt }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import topBar from '@/components/TopBar'
import wx from 'weixin-js-sdk'
export default {
  name: 'AboutUs',
  components: {
    topBar
  },
  data () {
    return {
      menus: ['关于我们', '管理团队', '联系我们'],
      isactive: 0,
      goodInfo: [
        {
          title: '技术优势',
          text: '保险专业理论和大数据及智能算法结合，构建保险知识图谱。以此为基础开发服务用户的应用工具，为前端应用提供深厚的技术支撑。'
        },
        {
          title: '产品优势',
          text: '覆盖全市场主流产品的保险数据库，三大标准组成的保险产品评价系统，全网选保、精准推荐的智能选保模型，自定义使用的保险产品评测工具；精算师严格把控选择的优秀保险产品组合。可视化的输出界面清晰易懂。'
        },
        {
          title: '团队优势',
          text: '我们的团队专注保险科技，热爱保险事业，重视用户需求，倾力服务用户。一群保险、IT、大数据、金融管理、客户服务的专业人士走到一起，只为捍卫用户家庭幸福。'
        }
      ],
      teamInfo: [
        // {
        //   photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_03.png',
        //   title: '谭锐：CEO',
        //   info: '多年金融机构管理、投资经验，曾任国内大型期货公司机构负责人，私募证券投资基金负责人，管理量化对冲基金。'
        // },
        {
          photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_06.png',
          title: '李辉：CTO',
          info: '毕业于南京大学计算机系，曾服务TP-LINK，江苏电信、新加坡GARENA，参与主导开发全国多个省份的公共服务系统，包括智能监测、火情、警情、道路交通信息系统等。'
        },
        {
          photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_08.png',
          title: '初昕欣：CPO',
          info: '中国人民大学商学院财务管理专业学士，美国伊利诺伊大学香槟分校精算专业硕士。曾服务于中国再保险等国内外知名再保险公司，拥有丰富的产险产品开发、定价及风险管理的相关经验。'
        },
        {
          photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_10.png',
          title: '曾途：联合创始人',
          info: '中国青年企业家协会常务理事，2015年四川省最具经济影响力人物奖获得者，入选2016年《财富》“定义未来的50位中国商业先锋”，四川省金融学会金融科技专委会主任委员，创建国内大数据龙头企业BBD。'
        },
        {
          photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_12.png',
          title: '吴大维：联合创始人',
          info: '专注金融创新与金融风险。曾任IBM全球大数据与分析行业资深顾问和中国区大数据分析解决方案负责人，IBM全球风险管理解决方案总监，安永（EY）金融行业金融转型和创新团队执行总监，银监会十二五规划风险管理专题外聘专家，中国人民银行特聘金融科技顾问，世界银行金融稳定工作小组成员。'
        },
        {
          photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_14.png',
          title: '代虓：产品经理',
          info: '中国人民大学保险系学士。曾服务于中国人寿、利宝保险、安盛天平，具有精算部、投资管理中心等核心部门的从业经历。拥有丰富的产品开发、策划、市场拓展的相关经验。'
        },
        {
          photo: 'https://cdn.aibaodata.com/zhenyi.png',
          title: '郑义：产品经理',
          info: '毕业于上海财经大学保险精算专业，英国精算协会会员，熟悉保险产品的定价、核保、保险机构数据库建设，曾对跨国再保险公司和保险机构进行过深入的案例分析。具有韦莱韬略中国、农业银行重庆分行等机构的保险、金融销售工作经验。'
        },
        {
          photo: 'http://cdn.aibaodata.com/%E5%85%B3%E4%BA%8E%E6%88%91%E4%BB%AC-%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F_16.png',
          title: '蒋倩：产品经理',
          info: '中国科学技术大学精密机械及机密仪器专业学士，韩国崇实大学精算学硕士。曾服务于中韩人寿，主要从事精算评估工作。拥有丰富的寿险精算评估、偿付能力管理及风险控制的相关经验。'
        }
      ],
      contact: [
        {
          ico: '#icon-attachment',
          txt: 'www.aibaodata.com'
        },
        {
          ico: '#icon-email',
          txt: 'pr@aibaodata.com'
        },
        {
          ico: '#icon-phone',
          txt: '400-0015-006'
        },
        {
          ico: '#icon-wechat',
          txt: 'aibaodata001'
        }
      ]
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.getWX()
    })
  },
  methods: {
    changeMenu (index) {
      this.isactive = index
    },
    getWX () {
      let nowUrl = window.location.href
      this.axios.get('/api/getWxToken', {
        params: {
          url: nowUrl
        }
      }).then((response) => {
        sessionStorage.appId = response.data.appID
        sessionStorage.timestamp = response.data.timestamp
        sessionStorage.nonceStr = response.data.nonceStr
        sessionStorage.signature = response.data.signature
        let wxUrl = window.location.href.replace('#', '~')
        this.startWX(wxUrl)
      })
    },
    startWX (url) {
      wx.config({
        debug: false,
        appId: sessionStorage.appId,
        timestamp: sessionStorage.timestamp,
        nonceStr: sessionStorage.nonceStr,
        signature: sessionStorage.signature,
        jsApiList: ['checkJsApi', 'onMenuShareTimeline', 'onMenuShareAppMessage']
      })
      wx.ready(function () {
        wx.checkJsApi({
          jsApiList: ['onMenuShareTimeline', 'onMenuShareAppMessage']
        })
        wx.onMenuShareTimeline({
          title: 'AI保数据 | 关于我们', // 分享标题
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
        // 朋友
        wx.onMenuShareAppMessage({
          title: 'AI保数据 | 关于我们', // 分享标题
          desc: 'AI保通过金融大数据和保险专业理论结合，用保险科技的方式帮助用户解决保险需求。', // 分享描述
          link: url, // 分享链接
          imgUrl: 'https://cdn.aibaodata.com/2017/12/14/logo.jpg'
        })
      })
      wx.error(function (res) {
        console.log(res)
      })
    }
  }
}
</script>

<style scoped>
@import '../../assets/css/global.css';
#aboutus {
  height: 100%;
  width: 100%;
}

.menu {
  width: 100%;
  height: 90px;
  line-height: 90px;
  background: var(--bgc);
  display: flex;
  text-align: center;
  font-size: var(--m-fs);
  color: var(--m-bgc);
  box-shadow: 0 0.083rem 0.167rem rgba(0,0,0,.06);
  & div {
    flex: 1;
  }
  & div:nth-child(2) {
    border-left: 2PX solid #e5e5e5;
    border-right: 2PX solid #e5e5e5;
  }
  & .active {
    border-bottom: 3PX solid var(--blue);
  }
}

.one {
  & .text {
    color: var(--m-bgc);
    font-size: var(--s-fs);
    padding: 20px 40px;
    text-indent: 2em;
    text-align: justify;
  }
  & .title {
    font-size: var(--b-fs);
    color: var(--d-bgc);
    text-align: center;
    font-weight: 700;
    position: relative;
    line-height: 2;
    &:after {
      content: "";
      position: absolute;
      left: 50%;
      bottom: 0;
      margin-left: -35px;
      width: 70px;
      height: 5px;
      background: var(--blue);
    }
  }
  & .text1 {
    background: var(--bgc);
    padding-bottom: 20px;
    & img {
      width: 100%;
      height: 200px;
    }
  }
  & .text2 {
    padding-top: 50px;
    & img {
      width: 100%;
      height: 300px;
    }
  }
  & .text3 {
    padding: 0 20px 20px;
    background: var(--bgc);
    & .goodness {
      background: var(--s-blue);
      display: flex;
      padding: 27px;
      margin-top: 20px;
      align-items:center;
      & .left {
        flex: 1;
        color: var(--m-bgc);
        text-align: center;
        & .icon {
          width: 120px;
          height: 120px;
          margin: 0 auto 10px;
          background: var(--s-blue);
          border-radius: 50%;
          background-size: 100% 100%;
        }
        & .icon0 {
          background-image: url("../../assets/img/about-icon1.png");
        }
        & .icon1 {
          background-image: url("../../assets/img/about-icon2.png");
        }
        & .icon2 {
          background-image: url("../../assets/img/about-icon3.png");
        }
      }
      & .right {
        flex: 3;
        margin-left: 20px;
        color: var(--s-bgc);
        font-size: var(--s-fs);
        text-align: justify;
      }
    }
  }
}

.two {
  & .list {
    display: flex;
    padding: 20px;
    & .left {
      margin-right: 40px;
      & img {
        width: 150px;
        height: 150px;
      }
    }
    & .right {
      padding-bottom: 40px;
      & .name {
        font-size: var(--m2-fs);
        margin-bottom: 10px;
      }
      & .label {
        font-size: var(--s-fs);
        color: var(--s-bgc);
      }
    }
  }
  & .list:not(:last-child) .right {
    border-bottom: 2PX solid #eee;
  }
}

.three {
  width: 100%;
  height: calc(100% - 180px);
  background: url('../../assets/img/contact-bg.jpg') center bottom no-repeat;
  background-size: 100% auto;
  & .blue {
    color: var(--blue);
  }
  & .info {
    display: flex;
    margin-bottom: 20px;
    padding-left: 60px;
    color: var(--m-bgc);
    font-size: var(--m-fs);
    & .ico {
      margin-right: 40px;
      & .icon {
        width: 35px;
        height: 35px;
      }
    }
  }
  & .info:nth-child(1) {
    padding-top: 60px;
  }
}
</style>
