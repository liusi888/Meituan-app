<template>
  <div class="header">
    <!--头部-->
    <div class="header-edit clearfix">
      <!--左侧头像-->
      <div class="portrait fl">
        <img width="64" height="64" :src="seller.avatar" />
      </div>
      <!--右侧内容-->
      <div class="fl brief">
        <div class="brand">
          <span class="brand-img"></span>
          <span class="brand-name">{{seller.name}}</span>
        </div>
        <div class="description">
          <span>{{seller.description}}/{{seller.deliveryTime}}分钟送达</span>
        </div>
        <div v-if="seller.supports" class="supports">
            <span class="icon" :class="classMap[seller.supports[0].type]"></span>
            <span class="supports-text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <!--右侧的5个-->
      <div v-if="seller.supports" class="support-content">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <!--公告-->
    <div class="bullenit-content">
      <span class="bullenit-title"></span><span class="bullenit-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <!--背景图片-->
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%"/>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default{
      //header是子组件  App.vue是父组件。 props是子组件用来接收父组件的东西
      props:{
        seller:{

        }
      },
      created() {
          //切换class
          this.classMap=['decrease','discount','special','invoice','guarantee'];
      }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  /*引入的mixin.styl是因为要设置小图片的大小，用于适配手机*/
  @import "../../common/stylus/mixin.styl"
  .fl
    float:left;
  .fr
    float:right;
  .cl:after,.clearfix:after{content:".";display:block;height:0;clear:both;visibility:hidden}.cl,.clearfix{zoom:1}

  .header
    color:#fff;
    background :rgba(7,17,27,0.5)
    position :relative
    overflow :hidden
    .header-edit
      font-size:0
      position :relative
      .portrait
        padding:24px 12px 18px 24px
        img
          border-radius :2px
      .brief
        padding-top:24px
        font-size:14px
        .brand
          margin-bottom:6px
          .brand-img
            display :inline-block
            width: 30px
            height: 18px
            bg-image('brand')
            background-size:30px 18px
            background-repeat :no-repeat
            vertical-align :top
          .brand-name
            margin-left: 6px
            font-size: 16px
            line-height: 18px
            font-weight: bold
        .description
          margin:10px 0 10px 0
          font-size :12px
        .supports
          .icon
            display :inline-block
            vertical-align :top
            width:12px
            height:12px
            margin-right: 4px
            background-size: 12px 12px
            background-repeat:no-repeat
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
          .supports-text
            line-height :12px
            font-size: 10px
            vertical-align :top
      .support-content
        position :absolute
        right:12px
        bottom: 14px
        padding:0 8px
        height: 24px
        line-height 24px
        border-radius :14px
        background :rgba(0,0,0,0.2)
        text-align :center
        .count
          font-size :10px
          vertical-align :top
        .icon-keyboard_arrow_right
          font-size:10px
          line-height :24px
          margin-left:2px
    .bullenit-content
      height :28px
      line-height :28px
      padding:0 24px 0 12px
      white-space :nowrap
      overflow :hidden
      text-overflow :ellipsis
      position relative
      background :rgba(7,17,27,0.2)
      .bullenit-title
        display :inline-block
        width :22px
        height :12px
        bg-image('bulletin')
        background-size :22px 12px
        repeat:no-repeat
        vertical-align :top
        margin-top:8px
      .bullenit-text
        vertical-align :top
        margin:0 4px
        font-size: 10px
      .icon-keyboard_arrow_right
        position absolute
        font-size:10px
        right: 12px
        top:8px
    .background
      position absolute
      top: 0
      left:0
      width: 100%
      height:100%
      z-index :-1
      filter :blur(10px)





</style>
