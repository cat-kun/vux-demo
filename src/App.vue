<template>
  <div id="app">
    <view-box>
      <x-header class="header" slot="header" :left-options="{showBack: false}" title="网易">
        <!-- 插槽方式（推荐） -->
        <div slot="left">直播</div>
        <div>网易2</div>
        <div slot="right">搜索</div>
      </x-header>

      <scroller :lock-y="true">
        <div class="tab">
          <tab>
            <tab-item selected>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
            <tab-item>啪啪</tab-item>
          </tab>
        </div>
      </scroller>

      <swiper :list="swiperList" v-model="swiperIndex" :loop="true"></swiper>

      <marquee class="marquee">
        <marquee-item v-for="item in marqueeList">{{ item }}</marquee-item>
      </marquee>

      <panel :list="panelList"></panel>

      <tabbar slot="bottom">
        <tabbar-item>
          <img src="./assets/logo.png" alt="" slot="icon">
          <span slot="label">首页</span>
        </tabbar-item>
        <tabbar-item>
          <img src="./assets/logo.png" alt="" slot="icon">
          <span slot="label">推荐</span>
        </tabbar-item>
        <tabbar-item>
          <img src="./assets/logo.png" alt="" slot="icon">
          <span slot="label">消息</span>
        </tabbar-item>
      </tabbar>
    </view-box>

    <router-view></router-view>
  </div>
</template>

<script>
import { ViewBox, XHeader, Tabbar, TabbarItem, Tab, TabItem, Scroller, Swiper, Marquee, MarqueeItem, Panel } from 'vux';

export default {
  name: 'app',
  components: {
    ViewBox,
    XHeader,
    Tabbar,
    TabbarItem,
    Tab,
    TabItem,
    Scroller,
    Swiper,
    Marquee,
    MarqueeItem,
    Panel
  },
  created (){
    // http://3g.163.com/touch/jsonp/sy/recommend/0-9.html
    // http://api.qm41.com/vphot/apphome?type=BigPicture&Token=a2b714e2-4c81-4500-934c-54acf631d5ca&phone=15875460426&userID=8080&type=%E6%91%84%E5%BD%B1%E5%B8%88;%20Hm_lvt_099c4471d680cb13eaca0dfa67c5d72b=1506650457;%20Hm_lpvt_099c4471d680cb13eaca0dfa67c5d72b=1506677369
    this.$jsonp('http://api.qm41.com/vphot/HomeWorks').then(data => {
      console.log(data);
      /**
       * {
        url: 'javascript:',
        img: 'https://static.vux.li/demo/1.jpg',
        title: '送你一朵fua'
      }
       */
      /* this.swiperList = data.focus.filter(item => {
        return item.addata === null;
      }).map(item => {
        return {
          url: item.link,
          img: item.picInfo[0].url,
          title: item.title
        }
      }); */

      // 滚动数据
      /* let a = this.marqueeList = data.live.filter(item => {
        return item.addata === null;
      }).map(item => {
        title: item.title
      });
      console.log(a); */

      // 首屏列表数据
      /* this.panelList = data.list.filter(item => {
          return item.addata === null;
        }).map(item => {
          return {
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest,
            url: item.link
          }
        }) */
      });

  },
  data() {
    // let dataList = [];
    // for(let i = 0; i < 10; i++){
    //   dataList.push({
    //     src: 'http://somedomain.somdomain/x.jpg',
    //     fallbackSrc: 'http://placeholder.qiniudn.com/60x60/3cc51f/ffffff',
    //     title: '标题一',
    //     desc: '由各种物质组成的巨型球状天体，叫做星球。星球有一定的形状，有自己的运行轨道。',
    //     url: '/component/cell'
    //   })
    // }
    return {
      swiperList: [],
      swiperIndex: 0,
      panelList: [],
      marqueeList: []
    }
  }
}
</script>

<style lang="less">
@import '~vux/src/styles/reset.less';
html,
body {
  margin: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

#app {
  height: 100%;
  .header {
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1;
  }
  .tab {
    width: 600px;
    margin-top: 46px;
  }
  .marquee {
    margin: 10px;
  }
}
</style>
