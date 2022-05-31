### 介绍
**前景：** app页面添加水印展示 \
**技术实现：**[watermark-dom](https://github.com/saucxs/watermark-dom)<br />
**完整代码：**[vue-watermark](https://github.com/LvPassenger/vue-watermark)<br />
**实现效果：** \
![7ik2j-ysu0j.gif](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7bfd7adba504401a8c7d127dbe34f77d~tplv-k3u1fbpfcp-watermark.image?)\
**功能描述：**<br />
- 添加、删除、更新水印

### 引入

方式一：（推荐，方便拓展）<br />
    在index.html引入相关文件： 

```js
<script type="text/javascript" src="./watermark.js"></script>
```

方式二：<br />
    npm包引入 \
    通过`npm install watermark-dom`获取水印组件包 \
    在vue文件中引入相关文件：
```js
import watermark from 'watermark-dom'
// 或者
var watermarkDom = require("watermark-dom")
```

### 使用
1. 初始化水印

```js
watermark.init({ watermark_txt: "测试水印，1021002301，测试水印，100101010111101" })
```
2. 手动加载水印
```js
watermark.load({ watermark_txt: "测试水印，1021002301，测试水印，100101010111101" })
```
3. 删除水印
```js
watermark.remove()
```


### **踩坑之旅**

难点一：使用npm包引入的方式来实现水印，发现显示不全<br />
- 对于带有中文符号的字符串显示是正常的，如 \
`测试水印，1021002301，测试水印，100101010111101`

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/66f5b6e33fec41a0bba37aea413b9584~tplv-k3u1fbpfcp-watermark.image?)
 
- 而对于全是英文单词或符号的字符串就会导致显示不全，如
`EB1DBDvlQHkV1653898831963@EB1_CSYSU9xMut_bNaHOOH7b_bCOvzFW6N_c4KDS061VmwH`

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff580c75079d4b52a0b1c68330ae6fb4~tplv-k3u1fbpfcp-watermark.image?) 

思路：
给生成的水印DOM添加样式，强制英文单词断行

```css
word-break: break-all;
```
尝试：<br />
1.观察生成的水印DOM

![1653983672(1).png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e19a8c9ab9ec498b8fd45f01ae234936~tplv-k3u1fbpfcp-watermark.image?)

于是尝试覆盖shadow-root元素中的样式，直接在shadow DOM中注入样式


```js
const style = document.createElement('style')
style.innerHTML = 'div { word-break: break-all; }'
const host = document.getElementById('wm_div_id')
host.shadowRoot.appendChild(style)
host.innerHTML = host.shadowRoot
```
PS：若不起作用，可以尝试将上面的代码放在一个`setTimeout()`函数中异步执行 \
注意：仅当Shadow DOM模式设置为“open”时它才会起作用.


2.查看效果：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7096ad042b344b26a8524bacc525f885~tplv-k3u1fbpfcp-watermark.image?)

看着像是解决了bug，然而在实际场景中，切换不同的页面会导致还是显示不全，具体原因咱也不知道，估计是这个插件的问题吧。


![u=1183543500,2455753574&fm=253&fmt=auto&app=120&f=GIF.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e774772cde114bf793b154ce3ede93bd~tplv-k3u1fbpfcp-watermark.image?)


解决：

1.本地引入封装的js文件 \
public/index.html:
```js
<script type="text/javascript" src="./watermark.js"></script>
```

2.解决报错

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d7f0be9af1e4f46b9a4676798512961~tplv-k3u1fbpfcp-watermark.image?)

找到报错行所在代码，排查发现MutationObserver重复定义了，于是注释掉该行代码即可

```js
const MutationObserver = window.MutationObserver || window.WebKitMutationObserver || window.MozMutationObserver;
```

3.watermark.js文件中`loadMark()`函数添加一行代码：

```js
mask_div.style['word-break'] = "break-all";
```

如图：

![1653986200(1).png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/13a4bf2e188543cfbb4fed9071bf824f~tplv-k3u1fbpfcp-watermark.image?)


难点二：清空不存在的水印报错

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c41bd160884d4e4892b9bfb10a99511c~tplv-k3u1fbpfcp-watermark.image?)

思路：使用`try-catch`语句捕获异常或直接设置水印为空字符串 \
方法一：
```js
try {
    watermark.remove()
} catch (error) {}
```
方法二：
```js
watermark.load({ watermark_txt: ` ` })
```
### **总结** 
原本是一个简单添加水印功能，奈何一开始方向没选对，耗费了大量时间解决bug，记录下本次踩坑之旅。



![641.webp](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ecce8197e9f4523893c3f85052435c3~tplv-k3u1fbpfcp-watermark.image?)
