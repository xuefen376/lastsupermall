<!-- Home -->
<template>
  <div id="home">
    <nav-bar class="home-nav">
      <template v-slot:center>
        <div>购物街</div>
      </template>
    </nav-bar>

    <scroll class="content" ref="scroll" 
    :probe-type="3"
     @scroll="contentScroll"
      :pull-up-load="true"
      >
       
        <home-swiper :banners="banners" />
        <recommend-view :recommends="recommends" />
        <feature-view />
        <tab-control
          class="tab-contral"
          :titles="['流行', '新款', '精选']"
          @tabClick="tabClick"
        />
        <goods-list :goods="showGoods" />
       
    </scroll>
    <back-top @click.native="backTop" v-show="isShowBackTop" />

    
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";
import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goods/GoodsList";
import Scroll from 'components/common/scroll/Scroll';
import BackTop from 'components/content/backTop/BackTop'

import HomeSwiper from "./childComps/HomeSwiper";
import RecommendView from "./childComps/RecommendView.vue";
import FeatureView from "./childComps/FeatureView.vue";

import { getHomeMultidata, getHomeGoods } from "network/home";



export default {
  components: {
    NavBar,
    TabControl,
    HomeSwiper,
    RecommendView,
    FeatureView,
    GoodsList,
    Scroll,
    BackTop
  },
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] },
      },
      currentType: "pop",
      isShowBackTop: false
    };

  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    },
  },
  created() {
    // 1.请求多个数据
    this.getHomeMultidata();
    // 2.请求商品数据
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");

    // 3.监听item中图片加载完成
      this.$bus.$on('itemImageLoad', () => {
          console.log("--------------")
      })
  },
  methods: {
    backTop(){
      this.$refs.scroll.scrollTo(0, 0)
    },
    /**
     * 事件监听相关的方法
     */
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = "pop";
          break;
        case 1:
          this.currentType = "new";
          break;
        case 2:
          this.currentType = "sell";
          break;
      }
    },
    contentScroll(position) {
      this.isShowBackTop = (-position.y) > 1000
    },
    // loadMore(){
    //   this.getHomeGoods(this.currentType)
    // },
    /**
     * 网络请求相关的方法
     */
    // 1.请求多个数据
    getHomeMultidata() {
      getHomeMultidata().then((res) => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    // 2.请求商品数据
    getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then((res) => {
        console.log(res);
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;

        
      });
    },
  },
 
};
</script>
<style scoped>
#home {
  /* padding-top: 44px; */
  height: 100vh;
  position: relative;
}
.home-nav {
  background-color: var(--color-tint);
  color: #fff;

  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}
.tab-contral {
  position: sticky;
  top: 44px;
  z-index: 9;
}
  /* .content {
  
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 49px;
  } */
.content {
    height: calc(100% - 93px);
   overflow: hidden;
   margin-top: 44px;

}
</style>