<template>
    <div id="app">
        <view-box>
            <x-header class="header" slot="header" :left-options="{showBack: false}" title="网易">
                <!-- 插槽方式（推荐） -->
                <div slot="left">直播</div>
                <div>网易2</div>
                <div slot="right">搜索</div>
            </x-header>

            <sc :lock-y="true">
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
            </sc>

            <scroller class="scroller" :on-refresh="refresh" :on-infinite="infinite" ref="myRef">
                <swiper
                    :list="swiperList"
                    v-model="swiperIndex"
                    :loop="true"
                    dots-class="custom-bottom"
                ></swiper>

                <marquee class="marquee">
                    <marquee-item v-for="item in marqueeList">{{ item }}</marquee-item>
                </marquee>

                <panel :list="panelList"></panel>
                <panel :list="moreDataList"></panel>
            </scroller>

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
    import {
        ViewBox,
        XHeader,
        Tabbar,
        TabbarItem,
        Tab,
        TabItem,
        Scroller as sc,
        Swiper,
        Marquee,
        MarqueeItem,
        Panel
    } from 'vux';

    const refreshKey = ['A', 'B01', 'B02', 'B03', 'B04', 'B05', 'B06', 'B07', 'B08', 'B09', 'B010'];
    let key = 0;
    let val = 'A';

    // 控制数据是否加载完成
    let initLoader = false;

    function getRefreshKey() {
        key++;
        if (key == refreshKey.length) {
            key = 0;
        }
        val = refreshKey[key];
    }

    let start = 0;
    let end = start + 9;

    export default {
        name: 'app',
        components: {
            ViewBox,
            XHeader,
            Tabbar,
            TabbarItem,
            Tab,
            TabItem,
            sc,
            Swiper,
            Marquee,
            MarqueeItem,
            Panel
        },
        created() {
            // http://3g.163.com/touch/jsonp/sy/recommend/0-9.html
            // http://api.qm41.com/vphot/apphome?type=BigPicture&Token=a2b714e2-4c81-4500-934c-54acf631d5ca&phone=15875460426&userID=8080&type=%E6%91%84%E5%BD%B1%E5%B8%88;%20Hm_lvt_099c4471d680cb13eaca0dfa67c5d72b=1506650457;%20Hm_lpvt_099c4471d680cb13eaca0dfa67c5d72b=1506677369
            this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html').then(data => {
                 console.log('data:', data);
                /*{
                    url: 'javascript:',
                        img: 'https://static.vux.li/demo/1.jpg',
                    title: '送你一朵fua'
                }*/
                this.swiperList = data.focus.filter(item => {
                    return item.addata === null && item.picInfo[0];
                }).map(item => {
                    return {
                        url: item.link,
                        img: item.picInfo[0].url,
                        title: item.title
                    }
                });

                // 滚动数据
                this.marqueeList = data.live.filter(item => {
                    console.log(item.picInfo[0]);
                    return item.addata === null && item.picInfo[0];
                }).map(item2 => {
                    title: item2.title
                });

                // 首屏列表数据
                this.panelList = data.list.filter(item => {
                    return item.addata === null && item.picInfo[0];
                }).map(item => {
                    return {
                        src: item.picInfo[0].url,
                        title: item.title,
                        desc: item.digest,
                        url: item.link
                    }
                });

                initLoader = true;
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
                marqueeList: [],
                moreDataList: []
            }
        },
        methods: {
            // 下拉刷新
            refresh() {
                // console.log(1)
                // 下拉数据加载
                getRefreshKey();
                this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html', {
                    miss: '00',
                    refresh: val
                }).then(data => {
                    // console.log(data);

//                    console.log(this.$refs.myRef);

                    // 首屏列表数据
                    this.panelList = data.list.filter(item => {
                        return item.addata === null && item.picInfo[0];
                    }).map(item => {
                        return {
                            src: item.picInfo[0].url,
                            title: item.title,
                            desc: item.digest,
                            url: item.link
                        }
                    });

                    //隐藏loading
                    this.$refs.myRef.finishPullToRefresh();

                    this.$vux.toast.text(`已成功刷新${this.panelList.length}条数据`, 'middle');
                });


            },
            // 上拉刷新
            infinite() {

//                console.log(initLoader);
                if (!initLoader) {
//                    this.$refs.myRef.finishPullToRefresh();
                    this.$refs.myRef.finishInfinite();
                    return;
                }
                this.$jsonp(`http://3g.163.com/touch/jsonp/sy/recommend/${start}-${end}.html`, {
                    miss: '00',
                    refresh: val
                }).then(data => {

                    let dataList = data.list.filter(item => {
                        return item.addata === null && item.picInfo[0];
                    }).map(item => {
                        return {
                            src: item.picInfo[0].url,
                            title: item.title,
                            desc: item.digest,
                            url: item.link
                        }
                    });

                    this.moreDataList = this.moreDataList.concat(dataList);

                    start += 10;
                    end = start + 9;
                    this.$refs.myRef.finishInfinite();
                })
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
        .weui-media-box__hd {
            width: 102px;
            height: 78px;
            img {
                width: 102px;
            }
        }
        .scroller {
            top: 90px;
        }
        .loading-layer {
            padding-bottom: 90px;
        }
    }
</style>
