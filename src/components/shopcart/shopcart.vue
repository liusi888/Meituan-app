<template>
  <div>
    <div class="shopcart">
      <!--加减内容-->
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'heightLight':totalCount>0}">
              <i class="icon-shopping_cart" :class="{'heightLight':totalCount>0}"></i>
            </div>
            <div class="number" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'heightLight':totalPrice>0}">¥{{totalPrice}}元</div>
          <div class="desc" >另需配送费¥{{deliverPrice}}元</div>
        </div>
        <div class="content-right" @click.stop="pay">
          <div class="pay" :class="payClass">{{payDesc}}</div>
        </div>
      </div>
      <!--小球动画-->
      <div class="ball-container">
        <!--小球-->
        <div v-for="ball in balls">
          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <!--购物车列表-->
      <transition name="fade-fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty()">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="food in selectFoods">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>¥{{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" v-on:cartAdd="cartAdd"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <!--购物车列表的背景色-->
    <transition name="slide-fade">
      <div class="list-mask" v-show="listShow" @click="hideList">

      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  //  引入滚动的标签库
  import BScroll from 'better-scroll';
  //  引入加减商品的小组件
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  //  底部购物车组件
  export default {
    props:['deliverPrice','minPrice','selectFoods'],
    components:{
      cartcontrol
    },
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
        dropBalls:[],
        fold:true   //购物车折叠状态 fold是折叠的意思
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
      },
      //判断购物车的商品列表展开还是折叠状态
      listShow() {
        if(!this.totalCount){
          this.fold=true;
          return false;
        }
        let show=!this.fold;
        if(show){
          this.$nextTick(() => {
            if(!this.scroll){
              this.scroll=new BScroll(this.$refs.listContent,{
                click:true
              })
            }else{
              this.scroll.refresh()
            }

          });
        }
        return show;
      }

    },
    methods:{
      //父组件shopcart监听子组件cartcontrol传送过来的dom对象（为加入商品的小球动画做准备）
      cartAdd(target) {
        this.drop(target)
      },
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
      beforeDrop(el) { /* 购物车小球动画实现 */
        console.log('购物车小球动画实现');
        let count = this.balls.length;
        while(count--) {
          let ball = this.balls[count];
          if(ball.show) {
            let rect = ball.el.getBoundingClientRect(); //元素相对于视口的位置
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22); //获取y
            el.style.display = '';
            el.style.webkitTransform = 'translateY(' + y + 'px)'; //translateY
            el.style.transform = 'translateY(' + y + 'px)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = 'translateX(' + x + 'px)';
            inner.style.transform = 'translateX(' + x + 'px)';
          }
        }
      },
      dropping(el, done) { /*重置小球数量  样式重置*/
        console.log('重置小球数量  样式重置');
        let rf = el.offsetHeight;
        el.style.webkitTransform = 'translate3d(0,0,0)';
        el.style.transform = 'translate3d(0,0,0)';
        let inner = el.getElementsByClassName('inner-hook')[0];
        inner.style.webkitTransform = 'translate3d(0,0,0)';
        inner.style.transform = 'translate3d(0,0,0)';
        el.addEventListener('transitionend', done);
      },
      afterDrop(el) { /*初始化小球*/
        let ball = this.dropBalls.shift();
        if(ball) {
          ball.show = false;
          el.style.display = 'none';
        }
      },
      //切换显示购物车列表
      toggleList() {
        if(!this.totalCount){
          return ;
        }
        this.fold=!this.fold
      },
      //清空购物车
      empty() {
        this.selectFoods.forEach((food) => {
          food.count=0;
        })
      },
      // 点击背景色，隐藏购物车列表
      hideList() {
        this.fold=true;
      },
      pay() {
        if(this.totalPrice<this.minPrice){
          return;
        }
        window.alert('暂时不做此功能')
      }

    },


  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
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
        transition: all .4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
        .inner
          width: 16px
          height :16px
          border-radius :50%
          background :rgb(0,160,200)
          transition: all .4s linear
    .shopcart-list
      position :absolute
      left: 0
      bottom: 48px
      z-index :-1
      width :100%
      .list-header
        height :40px
        line-height: 40px
        padding:0 18px
        background :#f3f5f7
        border-bottom :2px solid rgba(7,17,27,0.1)
        .title
          float:left
          color:rgb(7,17,27)
          font-size :14px
          font-weight :200
        .empty
          float:right
          color:rgb(0,160,200)
          font-size :12px
      .list-content
        padding:0 18px
        max-height :217px
        background :#fff
        overflow :hidden
        .food
          position :relative
          padding:12px 0
          box-sizing :border-box
          border-1px(rgba(7,17,27,0.1))
          .name
            line-height :24px
            font-size :14px
            color:rgb(7,17,27)
          .price
            position :absolute
            right :90px
            bottom :12px
            line-height :24px
            font-size :14px
            font-weight :700
            color:rgb(240,20,20)
          .cartcontrol-wrapper
            position:absolute
            right:0
            bottom:6px
  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height :100%
    z-index: 9
    backdrop-filter :blur(10px)
    opacity: 1;
    background :rgba(7,17,27,0.6)
    &.slide-fade-enter, &.slide-fade-leave-to
      opacity: 0;
      background :rgba(7,17,27,0)
</style>
