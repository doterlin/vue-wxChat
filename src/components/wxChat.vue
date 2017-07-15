//微信聊天可视化组件
//依赖scrollLoader组件, 依赖指令v-emotion（写在main.js）

//参数：
// width               组件宽度，默认450
// maxHeight           展示窗口最高高度, 默认900
// data                （必需）传入初始化数据
// contactAvatarUrl    好友头像url
// ownerAvatarUrl      微信主人头像url
// ownerNickname       微信主人昵称
// getUpperData        （必需）当滚动到上方时加载数据的方法，返回值要为Promise对象
// getUnderData        （必需）当滚动到下方时加载数据的方法，返回值同上

<style scoped>
    .container{ width: 100%; height: 100%;z-index: 100; position: fixed; left:0; top: 0; overflow: hidden;}
    .shadow{ position: absolute; top:0; left: 0; z-index: 100; width: 100%; height: 100%; background: #000; opacity: .2; }
    .window {box-shadow: 1px 1px 20px -5px #000; max-width: 450px; background: #F5F5F5; margin: 0 auto; overflow: hidden; padding: 0; height: 100%;position: relative;z-index: 101;}
    button{border:0; background:none; border-radius: 0;text-align: center;}
    button{outline:none;}
    .w100{width: 100%;}
    .mt5{margin-top: 5px;}
    .mt10{margin-top: 10px;}
    .mt20{margin-top: 20px;}
    .mb10{margin-bottom: 10px;}
    .mb20{margin-bottom: 20px;}
    .fs0{font-size: 0}
    .title{background: #000; text-align: center; color:#fff; width: 100%; height: 50px; line-height: 50px; font-size: 14px;}
    .loading,.no-more{text-align: center; color: #b0b0b0; line-height: 100px;}
    .no-more{line-height: 60px;}
    .pull-right{float: right;}
    .link-line{text-decoration: underline;}
    .message{
        /*height: 100%;*/
        padding: 10px 15px;
        /*overflow-y: scroll;*/
        background-color: #F5F5F5;
    }
    .message li {
        margin-bottom: 15px;
        left:0;
        position: relative;
        display: block;
    }
    .message .time {
        margin: 10px 0;
        text-align: center;
    }
    .message .text {
        display: inline-block;
        position: relative;
        padding: 0 10px;
        max-width: calc(100% - 75px);
        min-height: 35px;
        line-height: 2.1;
        font-size: 15px;
        padding: 6px 10px;
        text-align: left;
        word-break: break-all;
        background-color: #fff;
        color: #000;
        border-radius: 4px;
        box-shadow: 0px 1px 7px -5px #000;
    }
    .message .avatar {
        float: left;
        margin: 0 10px 0 0;
        border-radius: 3px;
        background: #fff;
    }
    .message .time>span {
        display: inline-block;
        padding: 0 5px;
        font-size: 12px;
        color: #fff;
        border-radius: 2px;
        background-color: #DADADA;
    }
    .message .system>span{
        padding: 4px 9px;
        text-align: left;
    }
    .message .text:before {
        content: " ";
        position: absolute;
        top: 9px;
        right: 100%;
        border: 6px solid transparent;
        border-right-color: #fff;
    }
    .message .self {
        text-align: right;
    }
    .message .self .avatar {
        float: right;
        margin: 0 0 0 10px;
    }
    .message .self .text {
        background-color: #9EEA6A;
    }

    .message .self .text:before {
        right: inherit;
        left: 100%;
        border-right-color: transparent;
        border-left-color: #9EEA6A;
    }
    .message .image{
        max-width: 200px;
    }
    img.static-emotion-gif, img.static-emotion {
        vertical-align: middle !important;
    }

    .an-move-left{
        left: 0;
        animation: moveLeft .7s ease;
        -webkit-animation:moveLeft .7s ease; 
    }
    .an-move-right{
        left: 0;
        animation: moveRight .7s ease;
        -webkit-animation:moveRight .7s ease; 
    }
    .bgnone{
        background: none;
    }
    @keyframes moveRight{
        0%{left:-20px; opacity: 0};
        100%{left:0; opacity: 1}
    }

    @-webkit-keyframes moveRight
    {
        0%{left:-20px; opacity: 0};
        100%{left:0px; opacity: 1}
    }
    @keyframes moveLeft{
        0%{left:20px; opacity: 0};
        100%{left:0px; opacity: 1}
    }

    @-webkit-keyframes moveLeft
    {
        0%{left:20px; opacity: 0};
        100%{left:0px; opacity: 1}
    }

    @media (max-width: 367px){
        .fzDInfo{width:82%;}
    }

</style>

<template>
    <div class="container">
        <div class="window" id="window-view-container" :style="{maxHeight: maxHeight + 'px'}">
            <!-- data is empty -->
            <div class="loading" v-if="dataArray && dataArray.length==0">
                <div style="margin-top: 300px;text-align:center; font-size: 16px;">
                    <span>未查找到聊天记录</span>
                </div>
            </div>

            <!-- loading -->
            <div class="loading" v-if="dataArray.length==0">
                <div style="margin-top: 300px;">
                        <div>加载中...</div>
                </div>
            </div>

            <div class="title" v-if="dataArray && dataArray.length>0">
                <p v-text="ownerNickname"></p>
            </div>
            <!-- main -->
            <ScrollLoader :minHeight="minHeight" @scroll-to-top="refresh" @scroll-to-botton="infinite" class="container-main" v-if="dataArray && dataArray.length>0" :style="{maxHeight: maxHeight-50 + 'px'}">
                <div class="message">
                    <ul>
                        <li v-for="(message, index) in dataArray" :key="index" :class="message.direction==2?'an-move-right':'an-move-left'">
                            <p class="time"> <span v-text="message.ctime"></span> </p>
                            <p class="time system" v-if="message.type==10000"> <span v-html="message.content"></span> </p>
                            <div :class="'main' + (message.direction==2?' self':'')" v-else>
                                <img class="avatar" width="45" height="45" :src="message.direction==2? ownerAvatarUrl: contactAvatarUrl">
                                <!-- 文本 -->
                                <div class="text" v-emotion="message.content" v-if="message.type==1"></div>

                                <!-- 图片 -->
                                <div class="text" v-else-if="message.type==2">
                                    <img :src="message.content" class="image" alt="聊天图片">
                                </div>

                                <!-- 其他 -->
                                <div class="text" v-else-if="message.type!=10000" v-text="'[暂未支持的消息类型:'+ message.type +']\n\r' + message.content">
                                   
                                </div>
                            </div>
                        </li>
                        
                    </ul>
                </div>

            </ScrollLoader>

        </div>

    </div>
</template>

<script>
    import ScrollLoader from './scrollLoader.vue';

    export default {
        name: "wxChat",

        components: {
            ScrollLoader
        },

        props: {
            ownerNickname: {
                type: String,
                default: 'Mystic Faces'
            }, 

            data: {
                type: Array,
            },

            width: {
                type: Number,
                default: 600,
            },
            
            maxHeight: {
                type: Number,
                default: 800
            },

            contactAvatarUrl: {
                type: String,
            },

            ownerAvatarUrl: {
                type: String,
            },

            getUpperData: {
                type: Function
            },

            getUnderData: {
                type: Function
            }
        },

        data() {
            return {
                isLaoding: true,

                isRefreshedAll: false,
                isLoadedAll: false,
                
                minHeight: 700,

                dataArray: [],

            }
        },

        created() {
 
            this.initData();
        },
        mounted(){
            //document.getElementsByTagName('body')[0].scrollTop=0;
            this.minHeight = document.getElementById('window-view-container').offsetHeight;
            this.maxHeight = document.getElementById('window-view-container').offsetHeight;
        },

        methods: {
            initData(){
                this.dataArray=this.dataArray.concat(this.data);
            },

            //向上拉刷新
            refresh(done) {
                var me = this;
                if(me.isRefreshedAll){ 
                    done(true);
                    return;
                }
                
                try {
                    this.getUpperData().then(function(data){
                        console.log(data)
                        if(data.length==0){
                            me.isRefreshedAll = true;
                            done(true);
                        }else{
                            me.dataArray = data.reverse().concat(me.dataArray); //倒序合并
                            done();
                        }
                    })
                } catch (error) {
                    console.error('wxChat: props "getUpperData" must return a promise object!')
                }

            },

            //向下拉加载
            infinite(done) {
                var me = this;
                if(me.isLoadedAll){
                    done(true);
                    return;
                }
                
                try {
                    this.getUnderData().then(function(data){
                        console.log(data)
                        if(data==0){
                            me.isLoadedAll = true;
                            done(true);
                        }else{
                            done();
                            me.dataArray = me.dataArray.concat(data); //直接合并
                        }
                    })
                } catch (error) {
                    console.error('wxChat: props "getUnderData" must return a promise object!')
                }
            }

        }
    }
</script>