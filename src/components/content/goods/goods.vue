<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
          <span class="text">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">¥{{food.price}}</span>
                  <span v-show="food.oldPrice" class="old">¥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" v-on:cartAdd="cartAdd"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!--v-ref这个方法可以访问到子组件shopcar-->
    <shopCar ref="shopCar" :selectFoods="selectFoods" :deliverPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopCar>
  </div>
</template>

<script type="text/ecmascript-6">
  //  引入滚动的标签库
  import BScroll from 'better-scroll';
  //  引入购物车组件
  import shopCar from '../../shopcart/shopcart.vue';
  //  引入加减商品的小组件
  import cartcontrol from '../../cartcontrol/cartcontrol.vue';
  const ERR_OK = 0;
  export default{
    props:{
      seller:{

      }
    },
    components:{
      shopCar,
      cartcontrol
    },
    data(){
      return{
        classMap:[],
        goods:[],
        listHeight:[], //列表每一块的高度
        scrollY:0,
      }
    },
    computed: {
      //计算右侧商品在哪个版块区域，并且返回改版块区域的下标
      currentIndex() {
        for(let i=0;i<this.listHeight.length;i++){
          let height1=this.listHeight[i];
          let height2=this.listHeight[i+1];
          if(!height2||(this.scrollY>=height1&&this.scrollY<height2)){
            return i;
          }
        }
        return 0;
      },
      //计算选中的商品
      selectFoods() {
        let foods=[];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            //这个地方的food.count相当于直接给food加了一个字段，方便后面的判断数量使用
            //这个地方的food.count是再cartcontrol里面进行了设置
            if(food.count){
              foods.push(food)
            }
          })
        })
        return foods;
      }
    },
    created(){
      this.classMap=['decrease','discount','special','invoice','guarantee'];
      this.$http.get('/api/goods').then((response)=>{
        response = response.body;
        if (response.erron === ERR_OK) {
          this.goods = response.data;
          //this._initScroll();  不能直接写，由于vue是异步的，当在调取这个方法的时候，数据还没有更新好，所以高度没有起作用，要使用$nextTick这个函数解决
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          })
        }
      });
      this.$on('cart-add',(target) => {
        this.functionName(target)
      });
    },
    methods: {
      //这个方法是用来计算右侧商品滚动区域的高度的
      _initScroll() {
        this.menuScroll=new BScroll(this.$refs.menuWrapper, {
          click:true
        });

        this.foodsScroll=new BScroll(this.$refs.foodsWrapper, {
          click:true,
          probeType:3
        });
        //做右侧的商品y轴的位置
        this.foodsScroll.on('scroll',(pos) => {
          //pos.y是滚动的y轴的距离
          this.scrollY=Math.abs(Math.round(pos.y));
        })
      },
      _calculateHeight() {
        //右侧商品的每一个大的区块（包括标题）
        let foodList=this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height=0;
        this.listHeight.push(height);
        for(let i=0;i<foodList.length;i++){
          let item=foodList[i];
          height+=item.clientHeight;
          this.listHeight.push(height);
        }
      },
      //点击左侧菜单，对应到右侧的商品区域
      selectMenu(index,event) {
        //浏览器的派生事件里面没有_constructed，而我们自己派生的点击事件里面有_constructed并且为true。
        //这一步判断是为了屏蔽掉pc端，出现了2次点击事件
        if(!event._constructed){
          return;
        }
        let foodList=this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let el=foodList[index];
        this.foodsScroll.scrollToElement(el,300)
      },
      _drop(target) {
        // 这样是可以调用到子组件shopcart的drop方法的，并且传递给子组件target
        //优化体验异步动画
        this.$nextTick(() =>{
          this.$refs.shopCar.drop(target)
        })
      },
      //父组件goods监听子组件cartcontrol传送过来的dom对象，然后再传给shopcar（为加入商品的小球动画做准备）
      cartAdd(target) {
        this._drop(target)
      }

    },


  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../../common/stylus/mixin.styl"
  .goods
    display :flex
    position :absolute
    top: 176px
    bottom :46px
    width :100%
    overflow :hidden
    .menu-wrapper
      flex:0 0 80px
      width: 80px    //这个地方一定要设置宽度，不然在安卓手机上有问题
      background :#f3f5f7
      .menu-item
        display :table
        height :54px
        width :56px
        padding:0 12px
        line-height :14px
        &.current
          position :relative
          margin-top: -1px
          z-index: 1
          background :#ffffff
          font-weight :700
          .text
            border-none()
        .icon
          display :inline-block
          vertical-align :top
          width:12px
          height:12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat:no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display :table-cell
          width :56px
          vertical-align :middle
          border-1px(rgba(7,17,27,0.2))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height :26px
        line-height :26px
        border-left:2px solid #d9dde1
        font-size :12px
        color:rgb(147,153,159)
        background :#f3f5f7
      .food-item
        display :flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7,17,27,0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 67px
          margin-right: 10px
        .content
          flex:1
          .name
            margin:2px 0 8px 0px
            height :14px
            line-height :14px
            font-size: 14px
            color:rgb(7,17,27)
          .desc,.extra
            line-height :10px
            font-size: 10px
            color:rgb(147,153,159)
          .desc
            margin-bottom: 8px
            line-height :12px
          .extra
            .count
              margin-right :12px
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
            right :0px
            bottom :12px
</style>
