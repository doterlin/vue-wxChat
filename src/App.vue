<template>
<wxChat 
  :data="wxChatData"
  :showShade="false"
  ownerNickname="Doterlin"
  :getUpperData="getUpperData"
  :getUnderData="getUnderData"
  :ownerAvatarUrl="ownerAvatarUrl"
  :contactAvatarUrl="contactAvatarUrl"
  :width="600">
</wxChat>
</template>

<script>
import wxChat from './components/wxChat.vue'

export default {
  name: 'app',
  data () {
    return {
      upperTimes:0,
      underTimes:0,
      ownerAvatarUrl: '//upload.jianshu.io/users/upload_avatars/3169607/e6cbf6c30ea4.png?imageMogr2/auto-orient/strip|imageView2/1/w/240/h/240',
      contactAvatarUrl: '//upload.jianshu.io/users/upload_avatars/4/21d526846127.png?imageMogr2/auto-orient/strip|imageView2/1/w/240/h/240',
      wxChatData: [{
        direction: 2,
        type: 1,
        content: '你好!![呲牙]',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 1,
        type: 1,
        content: '你也好。[害羞]',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 2,
        type: 1,
        content: '这是我的简历头像：',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 2,
        type: 2,
        content: '/src/assets/wyz.jpg',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 1,
        type: 1,
        content: '你开心就好。[微笑]',
        ctime: new Date().toLocaleString()
      }]
    }
  },
  components:{wxChat},

  methods:{
    getUpperData(){
      var me = this;
      return new Promise(function(resolve){
        setTimeout(function(){
          resolve([{
              direction: 2,
              type: 1,
              content: '向上滚动加载第 ' + me.upperTimes +' 条！',
              ctime: new Date().toLocaleString()
            }]
      
          )
        }, 1000);
        
        me.upperTimes++;
      })
    },

    getUnderData(){
      var me = this;
      return new Promise(function(resolve){
        setTimeout(function(){
          resolve(
            [{
              direction: 1,
              type: 1,
              content: '向下滚动加载第 ' + me.underTimes +' 条！',
              ctime: new Date().toLocaleString()
            }]
          )
        }, 1000)
        me.underTimes++;
      })
    }

  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
}

</style>
