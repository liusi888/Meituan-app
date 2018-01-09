<template>
  <div class="cartcontrol">
    <transition name="fade">
      <div class="car-decrease" v-show="food.count>0" @click.stop="decreaseCart($event)">
          <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="car-count" v-show="food.count>0">{{food.count}}</div>
    <div class="car-add icon-add_circle" @click.stop="addCart($event)"></div>

  </div>
</template>

<script type="text/ecmascript-6">
  //  这个组件是banner商品的右侧商品的加减号
  import Vue from 'vue'
  export default {
    props:['food',],
    created(){

    },
    data() {
      return{
      }
    },
    methods: {
      addCart(event) {
        if(!event._constructed){
          return;
        }
        if(!this.food.count){
          Vue.set(this.food,'count',1)
        }else{
          this.food.count++;
        }
        //子组件cartcontrol - > 父组件goods 一个dom对象（为加入商品的小球动画做准备）
        this.$emit('cartAdd', event.target);
      },
      decreaseCart(event) {
        if(!event._constructed){
          return;
        }
        if(this.food.count){
          this.food.count--;
        }
      },

    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size:0
    .car-decrease
      display :inline-block
      padding :6px
      &.fade-enter-active,&.fade-leave-active
        transition: all .4s ease
      &.fade-enter, &.fade-leave-to
        transform: rotate(180deg)
        opacity: 0
      .inner
        display :inline-block
        font-size: 24px
        line-height :24px
        color:rgb(0,160,220)
    .car-add
      display :inline-block
      font-size: 24px
      line-height :24px
      padding :6px
      color:rgb(0,160,220)
    .car-count
      display :inline-block
      vertical-align :top
      width: 12px
      padding-top: 6px
      line-height :24px
      text-align :center
      font-size :10px
      color:rgb(147,153,159);

</style>
