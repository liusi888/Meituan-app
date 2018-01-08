<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'heightLight':totalCount>0}">
            <i class="icon-shopping_cart" :class="{'heightLight':totalCount>0}"></i>
          </div>
          <div class="number" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'heightLight':totalPrice>0}">¥{{totalPrice}}元</div>
        <div class="desc">另需配送费¥{{deliverPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <transition
      name="fade"
      v-on:before-enter="beforeEnter"
      v-on:enter="enter"
      v-on:after-enter="afterEnter"
      v-bind:css="false"
    >
      <div class="ball-container">
        <div v-for="ball in balls" v-show="ball.show" class="ball"></div>
        <div class="inner inner-hook"></div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">

  //  底部购物车组件
  export default {
    props:['deliverPrice','minPrice','selectFoods'],
    data() {
      return {
        balls:[
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          }
        ],
        dropBalls:[]
      }
    },
    computed:{
      //计算商品的总价格
      totalPrice(){
        let total=0;
        this.selectFoods.forEach((food) =>{
          total+=food.price*food.count;
        })
        return total
      },
      //计算一共有几件商品
      totalCount(){
        let count=0;
        this.selectFoods.forEach((food) =>{
          count+=food.count;
        })
        return count
      },
      //计算购物车最右侧的3种状态
      payDesc(){
        if(this.totalPrice===0){
          //反引号是es6的新语法，不用我们再拼接字符串
          return `¥${this.minPrice}元起送`
        }else if(this.totalPrice<this.minPrice){
          let price=this.minPrice-this.totalPrice;
          return `还差¥${price}元起送`
        }else {
          return '去结算'
        }
      },
      //计算购物车最右侧的3种状态下的样式
      payClass(){
        if(this.totalPrice<this.minPrice){
          return 'not-enouth'
        }else {
          return 'enouth'
        }
      }
    },
    methods:{
      //这个drop的方法里面的el参数，是从父组件goods里面传过来的商品的右侧的点击加的按钮的dom对象
      drop(el) {
        for(let i=0;i<this.balls.length;i++){
          let ball=this.balls[i];
          if(!ball.show){
            ball.show=true;
            ball.el=el;
            this.dropBalls.push(ball);
            return;
          }

        }
      },
      // --------
      // 进入中
      // --------

      beforeEnter: function (el) {
        let count=this.balls.length;
        while(count--){
          let ball=this.balls[count];
          if(ball.show){
            let rect=ball.el.getBoundingClientRect();
            let x=rect.left-32;
            let y=-(window.innerHeight-rect.top-22);
            el.style.display=' ';
            el.style.webkitTransform=`translate3d(0,$(y)px,0)`;
            el.style.transform=`translate3d(0,$(y)px,0)`;
            let inner=el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform=`translate3d($(x)px,0,0)`;
            inner.style.transform=`translate3d($(x)px,0,0)`;
          }
        }
      },
      // 与 CSS 结合时使用
      enter: function (el,done) {
        let rf=el.offsetHeight;
        this.$nextTick(() =>{
          el.style.webkitTransform='translate3d(0,0,0)';
          el.style.transform='translate3d(0,0,0)';
          let inner=el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform='translate3d(0,0,0)';
          inner.style.transform='translate3d(0,0,0)';
        })

      },
      afterEnter: function (el) {
        let ball=this.dropBalls.shift();
        if(ball){
          ball.show=false;
          el.style.display='none';
        }
      },
    },


  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position :fixed
    left: 0
    bottom :0
    z-index: 10
    width :100%
    height :48px
    .content
      display :flex
      background :#141d27
      .content-left
        flex :1
        font-size:0
        .logo-wrapper
          display :inline-block
          position :relative
          top: -10px
          margin:0 12px
          padding: 6px
          width :56px
          height :56px
          box-sizing :border-box
          vertical-align :top
          border-radius :50%
          background :#141d27
          .logo
            width: 100%
            height :100%
            border-radius :50%
            background :#2b343c
            text-align :center
            &.heightLight
              background :rgb(0,160,220)
            .icon-shopping_cart
              line-height :44px
              font-size :24px
              color:#80858a
              &.heightLight
                color:#fff
          .number
            position :absolute
            top: 0
            right :0px
            width :24px
            height :16px
            line-height :16px
            text-align :center
            border-radius :16px
            font-size :9px
            font-weight :700
            color: #ffffff
            background :rgb(240,20,20)
            box-shadow :0 4px 8px 0 rgba(0,0,0,0.4)
        .price
          display :inline-block
          vertical-align :top
          margin-top:12px
          line-height :24px
          box-sizing :border-box
          padding-right :12px
          border-right :1px solid rgba(255,255,255,0.1)
          font-size:16px
          font-weight :700
          color:rgba(255,255,255,0.4)
          &.heightLight
            color:#fff
        .desc
          display :inline-block
          vertical-align :top
          line-height :24px
          margin:12px 0 0 12px
          color:rgba(255,255,255,0.4)
          font-size: 10px
      .content-right
        flex: 0 0 105px
        width :105px
        background :rgba(255,255,255,0.1)
        .pay
          height :48px
          line-height :48px
          text-align center
          font-size :12px
          font-weight :700
          color:rgba(255,255,255,0.4)
          &.not-enouth
            background :rgba(255,255,255,0.1)
          &.enouth
            background :#00b43c
            color:#fff

    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index:200
        &.fade-enter-active
          transition: all .4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
          .inner
            width: 16px
            height :16px
            border-radius :50%
            background :rgb(0,160,200)
            transition: all .4s linear


</style>
