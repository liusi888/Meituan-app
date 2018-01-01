<template>
  <div class="star" :class="starType">
    <span v-for="item in itemClass" :class="item" class="star-item" track-by="$index">

    </span>
  </div>
</template>

<script type="text/ecmascript-6">
  const LENGTH=5;
  const cla_on='on';
  const cla_half='half';
  const cla_off='off';
  //  这是一个显示分数星星的组件，决定有几颗星，取决于打分。在一个就是他的大小。所以props里面接收2个参数
  export default {
    props:{
      size:{
        type:Number
      },
      score:{
        type:Number
      }
    },
    computed:{
      // 计算星星的大小尺寸
      starType(){
        return 'star-'+this.size
      },
      // 计算星星的显示状态
      itemClass(){
        let result=[];
        let scroe=Math.floor(this.score*2)/2;
        let has=scroe%1 !==0;
        let integer=Math.floor(scroe);
        for(let i=0;i<integer;i++){
          result.push(cla_on);
        }
        if(has){
          result.push(cla_half);
        }
        if(result.length<LENGTH){
          result.push(cla_off);
        }
        return result;
      }
    }

  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .star
    font-size:0
    .star-item
      display :inline-block
      background-repeat :no-repeat
    &.star-48
      .star-item
        width: 20px
        height: 20px
        margin-right: 22px
        background-size :20px 20px
        &:last-clild
          margin-right:0
        &.on
          bg-image('star48_on')
        &.half
          bg-image('star48_half')
        &.off
          bg-image('star48_off')
    &.star-36
      .star-item
        width: 15px
        height: 15px
        margin-right: 16px
        background-size :15px 15px
        &:last-clild
          margin-right:0
        &.on
          bg-image('star36_on')
        &.half
          bg-image('star36_half')
        &.off
          bg-image('star36_off')
    &.star-24
      .star-item
        width: 10px
        height: 10px
        margin-right: 3px
        background-size :10px 10px
        &:last-clild
          margin-right:0
        &.on
          bg-image('star24_on')
        &.half
          bg-image('star24_half')
        &.off
          bg-image('star24_off')
</style>
