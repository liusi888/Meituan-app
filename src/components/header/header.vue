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
      <div v-if="seller.supports" class="support-content" @click="showDtail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <!--公告-->
    <div class="bullenit-content" @click="showDtail">
      <span class="bullenit-title"></span><span class="bullenit-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <!--背景图片-->
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%"/>
    </div>
    <!--模态框，使用css sticky footer布局,这种布局也是有套路的，具体的css样式看下面-->
    <div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix">
        <!--真正的内容层-->
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrap">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <!--这里面的这3个，要用div布局，如果用span回出现不对齐现象，这个地方的布局要考虑2个问题：1.线的自适应，2.文字的左右2侧间距，不能透出底下的线-->
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li class="supports-item" v-for="(item,index) in seller.supports">
              <span class="icon" :class="classMap[seller.supports[index].type]"></span>
              <span class="text">{{seller.supports[index].description}}</span>
            </li>
          </ul>
          <div class="title">
            <!--这里面的这3个，要用div布局，如果用span回出现不对齐现象，这个地方的布局要考虑2个问题：1.线的自适应，2.文字的左右2侧间距，不能透出底下的线-->
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star.vue'
  export default{
      //header是子组件  App.vue是父组件。 props是子组件用来接收父组件的东西
      props:{
        seller:{

        },
      },
      components: {
        star
      },
      data(){
        return{
          detailShow:false
        }
      },
      created() {
          //切换class
          this.classMap=['decrease','discount','special','invoice','guarantee'];
      },
      methods:{
        showDtail(){
          this.detailShow=true;
        },
        hideDetail(){
          this.detailShow=false;
        }
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


  .header
    color:#fff;
    background :rgba(7,17,27,0.5)
    position :relative
    overflow :hidden
    .header-edit
      font-size:0
      position :relative
      width:100%
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

    .detail
      position :fixed
      top: 0
      left:0
      z-index: 100
      width: 100%
      height: 100%
      overflow :auto
      background :rgba(7,17,27,0.8)
      //  以下是css sticky footer布局的套路，以后如果用这种布局直接拿走改数值就可以
      .detail-wrapper
        min-height :100%
        width :100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            line-height :16px
            text-align :center
            font-size :16px
            font-weight :700
          .star-wrap
            margin-top: 18px
            padding:2px 0px
            text-align :center
          .title
            display :flex
            width: 80%
            margin:28px auto 20px auto
            .line
              flex: 1
              position :relative
              top: -6px
              border-bottom :1px solid rgba(255,255,255,0.2)
            .text
              padding:0 12px
              font-weight :700
              font-size: 14px
          .supports
            width:80%
            margin:0 auto
            .supports-item
              padding:12px 0px
              margin-bottom:12px
              font-size: 0
              &:last-child
                margin-bottom :0px
              .icon
                display :inline-block
                width: 16px
                height: 16px
                vertical-align :top
                margin-right: 6px
                background-size :16px 16px
                background-repeat :no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
              .text
                line-height :16px
                font-size:12px
          .bulletin
            width: 80%
            margin:0 auto
            .content
              padding:0 12px
              font-size: 12px
              line-height :24px
      .detail-close
        position :relative
        width: 32px
        height 32px
        margin:-64px auto 0 auto
        clear :both
        font-size: 32px


</style>
