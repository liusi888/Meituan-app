<!--所有页面的集合点-->
<template>
  <div id="app">
    <Headss v-bind:seller="seller"></Headss>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <div class="main">
      <!-- 路由匹配到的组件将渲染在这里 -->
      <router-view :seller="seller"></router-view>
    </div>
    <div class="con">
      i am con
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Headss from './components/header/header.vue';

  const ERR_OK = 0;
  export default {
    components: {
      Headss
    },
    data() {
      return {
        seller: {}
      }
    },
    //声明周期
    created() {
      this.$http.get('/api/seller').then((response) => {
        //转成json  为什么response.body 是因为官网只有取body才能取到他的object
        response = response.body;
        if (response.erron === ERR_OK) {
          this.seller = response.data;
        }
      }, (response) => {

      })
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/mixin.styl"
  #app
    .tab
      display: flex;
      width: 100%;
      height: 40px;
      line-height 40px
      border-1px(rgba(7,17,27,0.1))
      .tab-item
        flex: 1;
        text-align: center;
        & > a
          display :block;
          font-size:14px;
          color:rgb(77,85,93)
        & > .router-link-active
            color:rgb(240,20,20)



</style>
