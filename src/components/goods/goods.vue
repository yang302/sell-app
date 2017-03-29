<template>
  <div class="goods">
    <div class='menu-wrapper' ref='menuWrapper'>
      <ul>
        <li v-for='(item,index) in goods' class='menu-item' :class='{"current": currentIndex === index}' @click='selectMenu(index,$event)'>
          <span class='text border-1px'>
            <span v-show='item.type>0' class='icon' :class='classMap[item.type]'></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class='foods-wrapper' ref='foodsWrapper'>
      <ul>
        <li v-for='item in goods' class='food-list food-list-hook'>
          <h1 class='title'>{{item.name}}</h1>
          <ul>
            <li v-for='food in item.foods' @click='selectFood(food, $event)' class='food-item border-1px'>
              <div class='icon'>
                <img width='57' height='57' :src="food.icon" alt="">
              </div>
              <div class='content'>
                <h2 class='name'>{{food.name}}</h2>
                <p class='desc'>{{food.description}}</p>
                <div class="extra">
                  <span class='count'>月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class='now'>￥{{food.price}}</span><span v-show='food.oldPrice' class='old'>￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food='food' @cartAdd='_drop'></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :selectFoods='selectFoods' :delivery-price='seller.deliveryPrice' :min-price='seller.minPrice' ref='shopcart'></shopcart>
    <food :food='selectedFood' ref='food' @cartAdd='_drop'></food>
  </div>
</template>

<script>
    import BScroll from 'better-scroll';
    import shopcart from 'components/shopcart/shopcart';
    import cartcontrol from 'components/cartcontrol/cartcontrol';
    import food from 'components/food/food';

    const ERR_OK = 0; //数据正常

    export default {
      props: {
        seller: {
          type: Object
        }
      },
      data() {
        return {
          goods: [],
          listHeight: [],
          scrollY: 0,
          selectedFood: {}
        };
      },
      computed: {
        currentIndex() {
          for(let i=0; i<this.listHeight.length; i++){
            let height1 = this.listHeight[i];
            let height2 = this.listHeight[i + 1];
            if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
              return i;
            }
          }
          return 0;
        },
        selectFoods() {
          let foods = [];
          this.goods.forEach((good) => {
            good.foods.forEach((food) => {
              if(food.count){
                foods.push(food);
              }
            });
          });
          return foods;
        }
      },
      created() {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];

        this.$http.get('/api/goods').then(response => {
          response = response.body;
          if(response.errno == ERR_OK){
            this.goods = response.data;
            this.$nextTick(() => {
              this._initScroll();
              this._calculateHeight();
            });
          }
        });
      },
      methods: {
        selectMenu(index, event) {
          if(!event._constructed){
            return;
          }
          let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
          let el = foodList[index];
          this.foodsScroll.scrollToElement(el, 300);
        },
        selectFood(food, event) {
          if(!event._constructed){
            return;
          }
          this.selectedFood = food;
          this.$refs.food.show();
        },
        _initScroll() {
          this.menuScroll = new BScroll(this.$refs.menuWrapper,{
            click: true
          });

          this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
            probeType: 3,
            click: true
          });

          this.foodsScroll.on('scroll', (pos) => {
            this.scrollY = Math.abs(Math.round(pos.y));
          });
        },
        _calculateHeight() {
          let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
          let height = 0;
          this.listHeight.push(height);
          for(let i=0; i<foodList.length; i++){
            let item = foodList[i];
            height += item.clientHeight;
            this.listHeight.push(height);
          }
        },
        _drop(target) {
          //体验优化，异步执行下落动画
          this.$nextTick(() => {
            this.$refs.shopcart.drop(target);
          });
        }
      },
      components: {
        shopcart,
        cartcontrol,
        food
      }
    };
</script>

<style lang='stylus' rel='stylesheet/stylus'> 
  @import 'goods';
</style>
