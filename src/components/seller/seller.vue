<template>
  <div class='seller' ref='seller'>
  <div>
    <div class='seller-content'>
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc">
          <star :size='36' :score='seller.score'></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class='remark'>
          <li class='block'>
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}元</span>
            </div>
          </li>
          <li class='block'>
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}元</span>
            </div>
          </li>
          <li class='block'>
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}分钟</span>
            </div>
          </li>
        </ul>
        <div class="favorite" @click='toggleFavorite($event)'>
          <i class='icon-favorite' :class='{"active": favorite}'></i>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class='content-wrapper'>
          <p class='content'>{{seller.bulletin}}</p>
        </div>
        <ul v-if='seller.supports' class='supports'>
          <li class='support-item border-1px' v-for='item in seller.supports'>
            <span class='icon' :class='classMap[item.type]'></span>
            <span class='text'>{{item.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class='pics'>
        <h1 class="title">商家实景</h1>
        <div class='pic-wrapper' ref='picWrapper'>
          <ul class='pic-list' ref='picList'>
            <li class='pic-item' v-for='pic in seller.pics'>
              <img :src="pic" width='120' height='90' alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class='info-item' v-for='info in seller.infos'>{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
  </div>
</template>

<script>
    import BScroll from 'better-scroll';
    import { saveToLocal, loadFromLocal } from 'common/js/store';
    import star from 'components/star/star';
    import split from 'components/split/split';

    export default {
      props: {
        seller: {
          type: Object
        }
      },
      data() {
        return {
          favorite: (() => {
            return loadFromLocal(this.seller.id, 'favorite', false);
          })()
        }
      },
      computed: {
        favoriteText() {
          return this.favorite ? '已收藏' : '收藏';
        }
      },
      created() {
        this._initScroll();
        this._initPics();
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      },
      methods: {
        _initScroll() {
          if(!this.scroll){
            this.$nextTick(() => {
              this.scroll = new BScroll(this.$el, {
                click: true
              });
            });
          }else{
            this.scroll.refresh();
          }
        },
        _initPics() {
          if(!this.picScroll){
            if(this.seller.pics){           
              this.$nextTick(() => {
                let picWidth = 120;
                let margin = 6;
                let width = (picWidth + margin) * this.seller.pics.length - margin;
                this.$refs.picList.style.width = width + 'px';
                this.picScroll = new BScroll(this.$refs.picWrapper, {
                  scrollX: true
                })
              });
            }
          }else{
              this.picScroll.refresh();
          }
        },
        toggleFavorite(event){
          if(!event._constructed){
            return;
          }
          this.favorite = !this.favorite;
          saveToLocal(this.seller.id, 'favorite', this.favorite);
        }
      },
      components: {
        star,
        split
      }
    };
</script>

<style lang='stylus' rel='stylesheet/stylus'> 
  @import 'seller';
</style>
