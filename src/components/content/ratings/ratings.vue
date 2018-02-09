<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <Star :size="36" :score="seller.serviceScore"></Star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <Star :size="36" :score="seller.foodScore"></Star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :rating="ratings" :desc="desc" @selectedType="selectType" @onlyContent="onlycheck"></ratingselect>
      <div class="ratings-wrapper">
        <ul>
          <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <Star :size="24" :score="rating.score"></Star>
                <span v-show="rating.deliveryTime" class="delivery">{{rating.deliveryTime}}分钟送达</span>
                <p class="text">{{rating.text}}</p>
                <div class="recommand" v-show="rating.recommend&&rating.recommend.length">
                  <span class="icon-thumb_up"></span>
                  <span class="item" v-for="recommend in rating.recommend">{{recommend}}</span>
                </div>
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Star from '../../star/star.vue';
  import ratingselect from '../../ratingselect/ratingselect.vue';
  import split from '../../split/split.vue';
  //  时间戳
  import {formatDate} from '../../../common/js/data';
  //  引入滚动的标签库
  import BScroll from 'better-scroll';

  const ERR_OK = 0;
  export default {
    props:['seller'],
    components:{Star,ratingselect,split},
    data () {
      return {
        ratings:[],
        onlyContent:true,
        desc:{
          all:'全部',
          positive:'满意',
          negative:'不满意'
        },
        Ocheck:false,
        Ttype:2,
      }
    },
    created() {
      this.$http.get('/api/ratings').then((response) =>{
        response=response.body;
        if (response.erron === ERR_OK) {
          this.ratings=response.data;
          this.$nextTick(()=>{
            this.scroll=new BScroll(this.$refs.ratings,{
              click:true
            })
          })
        }
      })
    },
    methods:{
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
  @import "../../../common/stylus/mixin.styl"
  .ratings
    position :absolute
    top: 174px
    bottom: 0
    left: 0
    width :100%
    overflow :hidden
    .overview
      display :flex
      padding:18px 0px
      .overview-left
        flex:0 0 137px
        width :137px
        border-right :1px solid rgb(7,17,27)
        text-align :center
        padding:6px 0
        @media screen and (max-width: 375px)
          flex:0 0 112px
          width :112px
        .score
          font-size: 24px
          line-height :28px
          color:rgb(255,153,0)
        .title
          font-size: 12px
          line-height :12px
          margin:12px 0
          color:rgb(7,17,27)
        .rank
          line-height :10px
          font-size:10px
          color:rgb(7,17,27)
      .overview-right
        flex:1
        padding:6px 0 6px 24px
        @media screen and (max-width: 375px)
          padding-left:6px
        .score-wrapper
          margin-bottom :8px
          font-size: 0
          .title
            font-size: 12px
            color:rgb(7,17,27)
            line-height :18px
          .star
            display :inline-block
            vertical-align :top
            margin:0 12px
            @media screen and (max-width: 375px)
              margin:0 6px
          .score
            font-size: 12px
            color:rgb(255,153,0)
            line-height :18px
        .delivery-wrapper
          font-size ：0
          .title
            font-size: 12px
            color:rgb(7,17,27)
            line-height :18px
          .delivery
            margin-left: 12px
            font-size: 12px
            color:rgb(147,153,159)
    .ratings-wrapper
      padding:0 18px
      .rating-item
        display :flex
        padding:18px 0px
        border-1px(rgba(7.17.27.0.1))
        .avatar
          flex :0 0 28px
          width :28px
          margin-right:12px
          img
            border-radius :50%
        .content
          flex: 1
          position :relative
          .name
            line-height :12px
            font-size :10px
            color:rgb(7,17,27)
            margin-bottom:4px
          .star-wrapper
            margin-bottom: 6px
            font-size: 0
            .star
              display :inline-block
              margin-right :6px
              vertical-align :top
            .delivery
              display :inline-block
              vertical-align :top
              color:rgb(147,153,159)
              font-size: 12px
            .text
              font-size: 12px
              line-height :18px
              color:rgb(7,17,27)
              margin: 8px 0
            .recommand
              line-height :12px
              font-size:0
              .icon-thumb_up,.item
                display :inline-block
                margin :0 8px 4px 0px
                font-size: 9px
              .icon-thumb_up
                color:rgb(0,160,220)
              .item
                padding:1px 6px
                border:1px solid rgba(2,17,27,0.2)
                border-radius :1px
                color:rgb(147,153,159)
                background :#fff
          .time
            position :absolute
            top: 0
            right 0
            font-size: 12px
            line-height :12px


</style>
