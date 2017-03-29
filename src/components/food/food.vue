<template>
    <transition name='move'>
        <div v-show='showFlap' class='food' ref='food'>
            <div class="food-content">
                <div class="image-header">
                    <img :src="food.image" alt="">
                    <div class='back' @click='hide'>
                        <i class='icon-arrow_lift'></i>
                    </div>
                </div>
                <div class="content">
                    <h1 class="title">{{food.name}}</h1>
                    <div class="detail">
                        <span class="sell-count">月售{{food.sellCount}}份</span>
                        <span class="rating">好评率{{food.rating}}%</span>
                    </div>
                    <div class='price'>
                        <span class='now'>￥{{food.price}}</span><span v-show='food.oldPrice' class='old'>￥{{food.oldPrice}}</span>
                    </div>
                     <div class="cartcontrol-wrapper">
                        <cartcontrol :food='food' @cartAdd='_drop'></cartcontrol>
                    </div>
                    <transition name='fade'>
                        <div @click.stop.prevent='addFirst' class="buy" v-show='!food.count || food.count === 0'>加入购物车</div>
                    </transition>
                </div>
                <split v-show='food.info'></split>
                <div class="info" v-show='food.info'>
                    <h1 class="title">商品介绍</h1>
                    <p class="text">{{food.info}}</p>
                </div>
                <split></split>
                <div class="rating">
                    <h1 class='title'>商品评价</h1>
                    <ratingselect @increment='incrementTotal' :select-type='selectType' :only-content='onlyContent' :desc='desc' :ratings='food.ratings'></ratingselect>
                    <div class="rating-wrapper">
                        <ul v-show='food.ratings && food.ratings.length'>
                            <li v-show='needShow(rating.rateType, rating.text)' v-for='rating in food.ratings' class='rating-item border-1px'>
                                <div class="user">
                                    <span class='name'>{{rating.username}}</span>
                                    <img class='avatar' width='12' height='12' :src="rating.avatar" alt="">
                                </div>
                                <div class="time">{{rating.rateTime | formatDate}}</div>
                                <p class="text">
                                    <span :class='{"icon-thumb_up": rating.rateType === 0, "icon-thumb_down": rating.rateType === 1}'></span>{{rating.text}}
                                </p>
                            </li>
                        </ul>
                        <div class='no-rating' v-show='!food.ratings || !food.ratings.length'>
                            暂无评价
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
    import Vue from 'vue';
    import BScroll from 'better-scroll';
    import { formatDate } from 'common/js/date';
    import cartcontrol from 'components/cartcontrol/cartcontrol';
    import split from 'components/split/split';
    import ratingselect from 'components/ratingselect/ratingselect';

    const POSITIVE = 0;
    const NEGATIVE = 1;
    const ALL = 2;

    export default {
        props: {
            food: {
                type: Object
            }
        },
        data() {
            return {
                showFlap: false,
                selectType: ALL,
                onlyContent: true,
                desc: {
                    all: '全部',
                    positive: '推荐',
                    negative: '吐槽'
                }
            };
        },
        methods: {
            show() {
                this.showFlap = true;
                this.selectType = ALL;
                this.onlyContent = false;
                this.$nextTick(() => {
                    if(!this.scroll){
                        this.scroll = new BScroll(this.$el, {
                            click: true
                        });
                    }else{
                        this.scroll.refresh();
                    }
                });
            },
            hide() {
                this.showFlap = false;
            },
            incrementTotal(type, data) {
                this[type] = data;
                this.$nextTick(() => {
                    this.scroll.refresh();
                });
            },
            addFirst(event) {
                if(!event._constructed){
                    return;
                }
                
                //子组件通过$emit触发父组件的方法
                this.$emit('cartAdd', event.target);

                Vue.set(this.food, 'count', 1);
            },
            _drop(target) {
                this.$emit('cartAdd', target); 
            },
            needShow(type, text) {
                if(this.onlyContent && !text){
                    return false;
                }
                if(this.selectType === ALL){
                    return true;
                }else{
                    return type === this.selectType;
                }
            }    
        },
        filters: {
            formatDate(time) {
                let date = new Date(time);
                return formatDate(date, 'yyyy-MM-dd hh:mm');
            }
        },
        components: {
            cartcontrol,
            split,
            ratingselect
        }        
    };
</script>

<style lang='stylus' rel='stylesheet/stylus'> 
  @import 'food';
</style>
