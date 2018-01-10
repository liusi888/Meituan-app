<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2,$event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{rating.length}}</span></span>
      <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div @click="toggleContent" class="switch">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE=0;  //正面评价
  const NEGATIVE=1;  //负面评价
  const ALL=2;        //所有评价
  export default {
    props:['rating','selectType','onlyContent','desc'],
//    props:{
//      rating:{
//        type:Array,
//        default() {
//          return []
//        }
//      },
//      selectType:{
//        type:Number,
//        default:ALL
//      },
//      onlyContent:{
//        type:Boolean,
//        default:false
//      },
//      desc:{
//        type:Object,
//        default() {
//          return{
//            all:'全部',
//            positive:'满意',
//            negative:'不满意'
//          }
//        }
//      }
//    },
    computed: {
      positives() {
        return this.rating.filter((rating) => {
            return rating.rateType === POSITIVE
        })
      },
      negatives() {
        return this.rating.filter((rating) => {
          return rating.rateType === NEGATIVE
        })
      }
    },
    methods:{
      select(type,event) {
        //这一步判断是为了屏蔽掉pc端，出现了2次点击事件
        if(!event._constructed){
          return;
        }
        this.selectType=type;
      },
      toggleContent() {
        if(!event._constructed){
          return;
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratingselect
    .rating-type
      padding: 18px 0px
      margin:0px 18px
      border-1px(rgba(7,17,27,0.1))
      font-size:0
      .block
        display :inline-block
        padding:8px 12px
        line-height :16px
        font-size: 12px
        border-radius :2px
        margin-right :8px
        color:rgb(77,85,93)
        &.active
          color:#fff
        .count
          font-size: 8px
          margin-left: 2px
        &.positive
          background :rgba(0,160,220,0.2)
          &.active
            background :rgb(0,160,220)
        &.negative
          background :rgba(77,85,93,0.2)
          &.active
            background :rgb(77,85,93)




    .switch
      padding:12px 18px
      line-height :24px
      border-bottom :1px solid rgba(7,17,27,0.1)
      color:rgb(147,153,159)
      font-size:0
      .icon-check_circle
        vertical-align :top
        font-size :24px
        margin-right: 4px
      .text
        vertical-align :top
        font-size:12px



</style>
