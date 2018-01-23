<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2,$event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{rat.length}}</span></span>
      <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div @click="toggleContent" class="switch">
      <span class="icon-check_circle" :class="{'active':this.onlyContent}"></span>
      <span class="text border-1px">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE=0;  //正面评价
  const NEGATIVE=1;  //负面评价
  const ALL=2;        //所有评价
  export default {
    //rating全部、吐槽的数据，是一个数组。从父级food传过来
    //desc 描述。表示的是：全部、吐槽。从父级food传过来
    //selectType 选中的状态，即选中的是全部还是吐槽。传给父级food.vue
    props:['rating','desc'],

    data() {
      return{
        selectType:2,
        onlyContent:false
      }
    },
    computed: {
      positives() {
        let a=this.rating;
        if(a==undefined){
          a=[];

        }else{
          let b=[];
          a.filter((rating) =>{
            if(rating.rateType===POSITIVE){
              b.push(rating.rateType)
            }
            a=b;
          })
        }
        return a;
      },
      negatives() {
        let a=this.rating;
        if(a==undefined){
          a=[];

        }else{
          let b=[];
          a.filter((rating) =>{
            if(rating.rateType===NEGATIVE){
              b.push(rating.rateType)
            }
            a=b;
          })
        }
        return a;
      },
      rat() {
        let a=this.rating;
        if(a== undefined){
          a=[];
        }else{
          a=this.rating;
        }
        return a
      }
    },

    methods:{
      //选中的全部还是吐槽的状态
      select(type,event) {
        //这一步判断是为了屏蔽掉pc端，出现了2次点击事件
        if(!event._constructed){
          return;
        }
        this.selectType=type;
        //把选择的状态传回父组件food
        this.$emit('selectedType',this.selectType);
      },
      //切换查看全部还是查看部分评论状态
      toggleContent() {
        if(!event._constructed){
          return;
        }
        this.onlyContent=!this.onlyContent;
        this.$emit('onlyContent',this.onlyContent);
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
        &.active
          background :rgb(0,160,220)
      .text
        vertical-align :top
        font-size:12px
        border-1px(rgba(7,17,27,0.1))



</style>
