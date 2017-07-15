// scrollLoader.vue
// 滚动加载组件

<style scoped>
   .container-main {margin: 0 auto; overflow: auto; overflow-x: hidden; padding: 0;}
   .loading{ width: 100%; height: 40px; position: relative; text-align: center; margin: 5px 0 ; color: #999; font-size: 13px;}
   .loading-icon{color: #707070;}
</style>

<template>
    <div id="scrollLoader-container" class="container-main">
        <div class="loading" v-if="topLoading">
            加载中...
        </div>

        <div :style="'min-height:' + realMinHeight + 'px; overflow-x:hidden'">
            <slot></slot>
        </div>

        <div class="loading" v-if="bottonLoading">
            加载中...
        </div>
    </div>
</template>

<script>
    export default {
        name: "scroll-loader",
        
        props: {
            //给slot传一个最小值，保证一开始能出现滚动条
            'minHeight': {
                type: Number,
                default: 800
            }, 
                
        },

        created(){
        },
        computed: {
            realMinHeight(){
                return this.minHeight + 30
            }
        },
        data() {
            return {
               topLoading: false,
               bottonLoading: false,

               stopTopLoading: false, //是否停止传播滚动到顶部事件
               stopBottonLoading: false,  //是否停止传播滚动到底部事件
            }
        },
        mounted(){
            this.listenScroll();
        },

        methods: {
            listenScroll(){
                var me = this;
                var topDone = (stopTopLoading) => {
                    me.topLoading = false;
                    if(stopTopLoading) me.stopTopLoading = true;
                };

                var bottonDone = (stopBottonLoading) => {
                    me.bottonLoading = false;
                    if(stopBottonLoading) me.stopBottonLoading = true;
                };
                setTimeout(function(){
                    var scrollContainer = document.getElementById('scrollLoader-container');

                    scrollContainer.onscroll = function(){

                        if(scrollContainer.scrollTop<=0 && !me.stopTopLoading){
                            if(me.topLoading) return;

                            me.topLoading = true;
                            me.$emit('scroll-to-top', topDone);
                        }
                        if((scrollContainer.offsetHeight + scrollContainer.scrollTop+1 >= scrollContainer.scrollHeight) && !me.stopBottonLoading){
                            if(me.bottonLoading) return;

                            me.bottonLoading = true;
                            scrollContainer.scrollTop += 40;
                            me.$emit('scroll-to-botton', bottonDone);
                        }
                    }
                }, 50)
            },

        }
    }
</script>