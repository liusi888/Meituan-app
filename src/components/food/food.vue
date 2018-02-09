<template>
    <div v-show="showFlag" class="food" ref="food">
      <transition name="fade">
        <div class="food1">
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
            <transition name="slider-fade">
              <div class="buy" v-show="!food.count || food.count ===0" @click="addFrist">加入购物车</div>
            </transition>
          </div>
          <split v-show="food.info"></split>
          <div class="info" v-show="food.info">
            <h1 class="title">商品信息</h1>
            <p class="text">{{food.info}}</p>
          </div>
          <split></split>
          <div class="rating">
            <h1 class="title">商品评价</h1>
            <ratingselect :rating="food.ratings" :desc="desc" @selectedType="selectType" @onlyContent="onlycheck"></ratingselect>
            <div class="rating-wrapper">
              <ul v-show="food.ratings && food.ratings.length">
                <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item border-1px">
                  <div class="user">
                     <div class="user-name">{{rating.username}}</div>
                     <img  class="avatar" width="12" height="12" :src="rating.avatar">
                  </div>
                  <div class="time">{{rating.rateTime | formatDate}}</div>
                  <p class="text">
                    <i :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></i>
                    <span>{{rating.text}}</span>
                  </p>
                </li>
              </ul>
              <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
            </div>
          </div>
        </div>
      </transition>
    </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';
  //  引入滚动的标签库
  import BScroll from 'better-scroll';
  //  引入加减商品的小组件
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  //  引入行高样式组件
  import split from '../split/split.vue';
  //  引入评论头部组件
  import ratingselect from '../ratingselect/ratingselect.vue';
  //  时间戳
  import {formatDate} from '../../common/js/data'


  const POSITIVE=0;  //正面评价
  const NEGATIVE=1;  //负面评价
  const ALL=2;        //所有评价
  export default {
    props:['food'],
    components:{
      cartcontrol,
      split,
      ratingselect,
    },
    data() {
      return {
        showFlag:false,
        onlyContent:true,
        desc:{
          all:'全部',
          positive:'推荐',
          negative:'吐槽'
        },
        Ttype:2,
        Ocheck:false,
      }
    },
    methods:{
      show() {
        //页面的显隐
        this.showFlag=true;
        //评论区默认显示
//        this.selectType=ALL;
//        this.onlycheck=false;
        this.$nextTick(() => {
          if(!this.scroll){
            this.scroll=new BScroll(this.$refs.food,{
              click:true
            })
          }else{
            this.scroll.refresh()
          }

        });
      },
      hide() {
        this.showFlag=false;
      },
      addFrist(event) {
        if(!event._constructed){
          return;
        }
        console.log(event.target);
        //子组件cartcontrol - > 父组件goods 一个dom对象（为加入商品的小球动画做准备）
        this.$emit('cartAdd', event.target);
        Vue.set(this.food,'count',1);
      },
      //从子组件传回来的选中的什么状态。是全部还是吐槽还是什么
      selectType(type) {
        this.Ttype=type;
      },
      //从子组件传回来的状态。是查看全部评论还是只查看部分评论
      onlycheck(check) {
        this.Ocheck=check;
      },
      //这个方法是用来过滤该条评论是否显示
      //type是关联的选中的是全部还是吐槽   text关联的是选中的是全部还是部分
      needShow(type,text) {
        if(this.Ocheck && !text){
          return false;
        }
        if(this.Ttype === 2){
          return true;
        }else{
          return type === this.Ttype;
        }
      }
    },
    filters: {
      formatDate (time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
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
    .food1
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
      .info
        padding: 18px
        .title
          line-height :14px
          margin-bottom: 6px
          font-size: 14px
          color:rgb(7,17,27)
        .text
          line-height :24px
          padding:0 8px
          font-size :12px
          color:rgb(77,85,93)
      .rating
        padding-top: 18px
        .title
          line-height :14px
          margin-left: 18px
          font-size: 14px
          color:rgb(7,17,27)
        .rating-wrapper
          padding:0 18px
          .rating-item
            position :relative
            padding:16px 0px
            border-1px(rgba(7.17.27.0.1))
            .user
              position :absolute
              right :0
              top: 16px
              font-size: 0
              line-height :12px
              .user-name
                display :inline-block
                vertical-align :top
                margin-right :6px
                font-size :10px
                color:rgb(147,153,159)
              .avatar
                border-radius :50%


            .time
              margin-bottom:6px
              font-size: 10px
              line-height :12px
              color:rgb(143,153,159)
            .text
              line-height :16px
              font-size: 12px
              color:rgb(7,17,27)
              .icon-thumb_down,.icon-thumb_up
                line-height :16px
                margin-right :4px
                font-size: 12px
              .icon-thumb_up
                color:rgb(0,160,220)
              .icon-thumb_down
                color:rgb(143,153,159)




          .no-rating
            padding:16px 0
            font-size:12px
            color:rgb(147,153,159)



</style>
