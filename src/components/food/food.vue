<template>
  <transition name="fade">
    <div v-show="showFlag" class="food" ref="food">
      <div class="image-header">
        <img :src="food.image" />
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="now">¥{{food.price}}</span>
          <span v-show="food.oldPrice" class="old">¥{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div class="buy" v-show="!food.count || food.count ===0">加入购物车</div>
      </div>



    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  //  引入滚动的标签库
  import BScroll from 'better-scroll';
  //  引入加减商品的小组件
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  export default {
    props:['food'],
    components:{
      cartcontrol
    },
    data() {
      return {
        showFlag:false
      }
    },
    methods:{
      show() {
        this.showFlag=true;
        this.$nextTick(() => {
          if(!this.scroll){
            this.scroll=new BScroll(this.$refs.food, {
              click:true
            });
          }else{
            this.scroll.refresh()
          }
        })
      },
      hide() {
        this.showFlag=false;
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .food
    position:fixed
    top: 0
    left: 0
    bottom: 48px
    z-index: 8
    width: 100%
    background :#fff
    transition :all 0.2s linear
    transform:translate3d(0,0,0)
    &.fade-enter,&.fade-leave-to
      transform:translate3d(100%,0,0)
    .image-header
      position :relative
      /*height先设0，这时padding-top为100%是相对width设置的100%；这样做是为了避免一进入页面，图片没有显示出来，为空。后加载出来以后为图的高度，屏幕会出现抖动效果*/
      width :100%
      height :0px
      padding-top: 100%
      img
        position :absolute
        top: 0
        left :0
        width :100%
        height :100%
      .back
        position :absolute
        top: 10px
        left: 0
        .icon-arrow_lift
          display :block
          padding:10px
          font-size :20px
          color:#fff

    .content
      padding: 18px
      position :relative
      .title
        font-size: 14px
        font-weight :700
        margin-bottom: 8px
        line-height :14px
        color:rgb(7,17,27)
      .detail
        margin-bottom: 18px
        line-height :10px
        font-size:0
        height :10px
        .sell-count,.rating
          font-size:10px
          color:rgb(147,153,159)
        .sell-count
          margin-right:12px
      .price
        font-weight :700
        line-height :24px
        .now
          margin-right :8px
          font-size: 10px
          color:rgb(240,20,20)
        .old
          text-decoration:line-through
          font-size: 10px
          color:rgb(147,153,159)
      .cartcontrol-wrapper
        position :absolute
        right: 12px
        bottom: 12px
      .buy
        position :absolute
        right: 18px
        bottom :18px
        z-index: 7
        height :24px
        line-height :24px
        padding:0 12px
        box-sizing :border-box
        border-radius :12px
        font-size:10px
        color: #ffffff
        background :rgb(0,160,220)
</style>
