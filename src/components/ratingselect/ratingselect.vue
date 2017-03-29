<template>
  <div class='ratingselect'>
    <div class="rating-type border-1px">
        <span @click='select(2, $event)' class='block positive' :class='{"active": selectType === 2}'>{{desc.all}}<span class='count'>{{ratings.length}}</span></span>
        <span @click='select(0, $event)' class='block positive' :class='{"active": selectType === 0}'>{{desc.positive}}<span class='count'>{{positives.length}}</span></span>
        <span @click='select(1, $event)' class='block negative' :class='{"active": selectType === 1}'>{{desc.negative}}<span class='count'>{{negatives.length}}</span></span>
    </div>  
    <div @click='toggleContent($event)' class="switch" :class='{"on": onlyContent}'>
        <i class='icon-check_circle'></i>
        <span class="text">只有内容的评价</span>
    </div>
  </div>
</template>

<script>
    const POSITIVE = 0;
    const NEGATIVE = 1;
    const ALL = 2;

    export default {
        data() {
            return {
                myselectType: this.selectType,
                myonlyContent: this.onlyContent
            }
        },
        props: {
            ratings: {
                type: Array,
                default() {
                    return [];
                }
            },
            selectType: {
                type: Number,
                default: ALL
            },
            onlyContent: {
                type: Boolean,
                default: false
            },
            desc: {
                type: Object,
                default() {
                    return {
                        all: '全部',
                        positive: '满意',
                        negative: '不满意'
                    }
                }
            }
        },
        computed: {
            positives() {
                return this.ratings.filter((rating) => {
                    return rating.rateType === POSITIVE;
                });
            },
            negatives() {
                return this.ratings.filter((rating) => {
                    return rating.rateType === NEGATIVE;
                });
            }
        },
        methods: {
            select(type, event) {
                if(!event._constructed){
                    return;
                }
                this.myselectType = type;
                this.$emit('increment', 'selectType', type);
            },
            toggleContent(event) {
                if(!event._constructed){
                    return;
                }
                this.myonlyContent = !this.myonlyContent;
                this.$emit('increment', 'onlyContent', this.myonlyContent);
            }
        }
    };
</script>

<style lang='stylus' rel='stylesheet/stylus'> 
  @import 'ratingselect';
</style>
