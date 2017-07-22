# vue-wxchat

> A WeChat front-end view component

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
> 源码：[https://github.com/doterlin/vue-wxChat](https://github.com/doterlin/vue-wxChat)
> 演示地址：[https://doterlin.github.io/vue-wxChat/](https://doterlin.github.io/vue-wxChat/)

![演示截图](http://upload-images.jianshu.io/upload_images/3169607-74793bd3d76c65c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 运行

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```
## 介绍
+ 支持文本和图片的展示（后续将支持对`语音类`的展示）。
+ 支持`滚动加载数据`，其中滚动加载依赖我另外一个组件scrollLoader.vue（《[Vue.js上下滚动加载组件](http://www.jianshu.com/p/1a1476a0eab5)》）。
+ 支持QQ表情，为了能使用表情请全局注册指令`v-emotion`,我在`main.js`做了实现；代码如下：

```
function toEmotion(text, isNoGif){
    var list = ['微笑', '撇嘴', '色', '发呆', '得意', '流泪', '害羞', '闭嘴', '睡', '大哭', '尴尬', '发怒', '调皮', '呲牙', '惊讶', '难过', '酷', '冷汗', '抓狂', '吐', '偷笑', '愉快', '白眼', '傲慢', '饥饿', '困', '惊恐', '流汗', '憨笑', '大兵', '奋斗', '咒骂', '疑问', '嘘', '晕', '折磨', '衰', '骷髅', '敲打', '再见', '擦汗', '抠鼻', '鼓掌', '糗大了', '坏笑', '左哼哼', '右哼哼', '哈欠', '鄙视', '委屈', '快哭了', '阴险', '亲亲', '吓', '可怜', '菜刀', '西瓜', '啤酒', '篮球', '乒乓', '咖啡', '饭', '猪头', '玫瑰', '凋谢', '示爱', '爱心', '心碎', '蛋糕', '闪电', '炸弹', '刀', '足球', '瓢虫', '便便', '月亮', '太阳', '礼物', '拥抱', '强', '弱', '握手', '胜利', '抱拳', '勾引', '拳头', '差劲', '爱你', 'NO', 'OK', '爱情', '飞吻', '跳跳', '发抖', '怄火', '转圈', '磕头', '回头', '跳绳', '挥手', '激动', '街舞', '献吻', '左太极', '右太极', '嘿哈', '捂脸', '奸笑', '机智', '皱眉', '耶', '红包', '鸡'];
    if (!text) {
        return text;
    }

    text = text.replace(/\[[\u4E00-\u9FA5]{1,3}\]/gi, function(word){
        var newWord = word.replace(/\[|\]/gi,'');
        var index = list.indexOf(newWord);
        var backgroundPositionX = -index * 24
        var imgHTML = '';
        if(index<0){
            return word;
        }

        if (isNoGif) {
            if(index>104){
                return word;
            }
            imgHTML = `<i class="static-emotion" style="background:url(https://res.wx.qq.com/mpres/htmledition/images/icon/emotion/default218877.gif) ${backgroundPositionX}px 0;"></i>`
        } else {
            var path = index>104 ? '/img' : 'https://res.wx.qq.com/mpres/htmledition/images/icon';
            imgHTML = `![](${path}/emotion/${index}.gif)`
        }
        return imgHTML;
    });
    return text;
}


Vue.directive('emotion', {
    bind: function (el, binding) {
        el.innerHTML = toEmotion(binding.value)
    }
});
```

##如何使用?
参数已经在组件中做了说明，并在`App.vue`中做了演示：
参数和说明：
**微信聊天可视化组件**
**依赖scrollLoader组件, 依赖指令v-emotion（实现请查看main.js）**

**参数**：
 `width`               组件宽度，默认450
 `wrapBg`              外层父元素背景颜色，默认#efefef
 `maxHeight`           展示窗口最高高度, 默认900
 `contactAvatarUrl`    好友头像url
`ownerAvatarUrl`      微信主人头像url
 `ownerNickname`       微信主人昵称
 `getUpperData`        （必需）当滚动到上方时加载数据的方法，返回值要为Promise对象，resolve的结构同data
`getUnderData`        （必需）当滚动到下方时加载数据的方法，返回值同上
`data`                （必需）传入初始化数据， 结构如下：
```
[{
    direction: 2, //为2表示微信主人发出的消息，1表示联系人
    id: 1, //根据这个来排序消息
    type: 1, //1为文本，2为图片
    content: '你好!![呲牙]', //当type为1时这里是文本消息，当type2为2时这里要存放图片地址；后续会支持语音的显示
    ctime: new Date().toLocaleString() //显示当前消息的发送时间
},
{
    direction: 1,
    id: 2,
    type: 1,
    content: '你也好。[害羞]',
    ctime: new Date().toLocaleString()
}]
```
## 完整的使用实例
效果：[https://doterlin.github.io/vue-wxChat/](https://doterlin.github.io/vue-wxChat/)
代码：
```
//主文件，对wxChat的用法做示例

<template>
<wxChat 
  :data="wxChatData"
  :showShade="false"
  contactNickname="简叔"
  :getUpperData="getUpperData"
  :getUnderData="getUnderData"
  :ownerAvatarUrl="ownerAvatarUrl"
  :contactAvatarUrl="contactAvatarUrl"
  :width="420">
</wxChat>
</template>

<script>
import wxChat from './components/wxChat.vue'

export default {
  name: 'app',
  data () {
    return {
      upperTimes: 0,
      underTimes: 0,
      upperId: 0,
      underId: 6,
      ownerAvatarUrl: './src/assets/avatar1.png',
      contactAvatarUrl: './src/assets/avatar2.png',
      wxChatData: [{
        direction: 2,
        id: 1,
        type: 1,
        content: '你好!![呲牙]',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 1,
        id: 2,
        type: 1,
        content: '你也好。[害羞]',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 2,
        id: 3,
        type: 1,
        content: '这是我的简历头像：',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 2,
        id: 4,
        type: 2,
        content: './src/assets/wyz.jpg',
        ctime: new Date().toLocaleString()
      },
      {
        direction: 1,
        id: 5,
        type: 1,
        content: '你开心就好。[微笑]',
        ctime: new Date().toLocaleString()
      }]
    }
  },
  components:{wxChat},

  methods:{

    //向上滚动加载数据
    getUpperData(){
      var me = this;
      
      // 这里为模拟异步加载数据
      // 实际上你可能要这么写:
      // return axios.get('xxx').then(function(result){
      //     return result;  //result的格式需要类似下面resolve里面的数组
      // })
      return new Promise(function(resolve){
        setTimeout(function(){
           //模拟加载完毕
          if(me.upperTimes>3){
            return resolve([]);
          }
          
          //加载数据
          resolve([{
              direction: 2,
              id: me.upperId-1,
              type: 1,
              content: '向上滚动加载第 ' + me.upperTimes +' 条！',
              ctime: new Date().toLocaleString()
            },
            {
              direction: 1,
              id: me.upperId-2,
              type: 1,
              content: '向上滚动加载第 ' + me.upperTimes +' 条！',
              ctime: new Date().toLocaleString()
            }]
      
          )
        }, 1000);
        me.upperId= me.upperId+2;
        me.upperTimes++;
      })
    },

    getUnderData(){
      var me = this;

      //意义同getUpperData()
      return new Promise(function(resolve){
        setTimeout(function(){
          //模拟加载完毕
          if(me.underTimes>3){
            return resolve([]);
          }
          
          //加载数据
          resolve(
            [{
              direction: 1,
              id: me.underId+1,
              type: 1,
              content: '向下滚动加载第 ' + me.underTimes +' 条！',
              ctime: new Date().toLocaleString()
            },
            {
              direction: 2,
              id: me.underId+2,
              type: 1,
              content: '向下滚动加载第 ' + me.underTimes +' 条！',
              ctime: new Date().toLocaleString()
            }]
          )
        }, 1000);

        me.underId = me.underId+2;
        me.underTimes++;
      })
    }

  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
}
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

```
欢迎 start:
[https://github.com/doterlin/vue-wxChat](https://github.com/doterlin/vue-wxChat)
