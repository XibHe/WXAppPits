# 微信小程序填坑记录

## 在wxml文件中使用text标签时内容不能与标题对齐的问题

```xml
  <view class="mod t-sign-time">
          <text>商品条码</text>
          <text class="value">
          67882932222222222</text>
  </view>
```
微信编译器识别到text中含有空行导致。修改如下:

```xml
  <view class="mod t-sign-time">
          <text>商品条码</text>
          <text class="value">67882932222222222r33ddfdfdfdf</text>
 </view>
```

## 文本无法居中

当设置display为inline-block时，遇到文本内容设置text-align: center始终无法居中的问题。虽然设置display: flex可以解决文本居中的问题，但整体View上的多个控件布局会乱。最后设置，

```css
line-height: 31px;
```
解决问题。(其中，31px为该视图控件的高度。) 通过设置line-height确定了显示文本内容的行高。

## justify-content属性

当一个视图上有多个控件，且这些控件处在一行时。使用justify-content可以很方便的控制这些控件的对齐方式，间距等样式。

.wxml

```xml
 <view class="drawer_title">
     <view class="btn_cancel" bindtap="powerDrawer" data-statu="close">取消</view>
     <text>剂型选择</text>
     <view class="btn_sure" bindtap="powerDrawer" data-statu="close">确定</view>
  </view>  
```

.wxss

```css
.drawer_title{  
  padding:15px;  
  font: 10px;  
  justify-content: space-between;
  border-bottom: 1px solid #E8E8EA; 
  flex-direction: row;
  display: flex;
}  
```
其中，justify-content: space-between为等间距，justify-content: space-around为平分间距。

## 小程序中的循环列表，在点击时改变当前项的背景颜色
.wxml

```xml
  <view class="drawer_content">
     <block wx:for="{{['注射剂1', '注射剂2', '注射剂3', '注射剂4', '注射剂', '注射剂', '注射剂','注射剂', '注射剂', '注射剂', '注射剂', '注射剂', '注射剂', '注射剂']}}">
        <text data-index="{{index}}" class="view-list {{index ==activeItemIndex ? 'active':''}}" bindtap="formTapClick">{{item}}</text>
     </block>
  </view>  

```
其中，默认未选中的样式为view-list，需要在class中默认写上。即，

```css
class="view-list {{index ==activeItemIndex ? 'active':''}}"
```
active为选中时改变背景需要调用的样式。

### 参考资料

[小程序中的循环列表，在点击时改变当前项的背景颜色](http://www.wxapp-union.com/portal.php?mod=view&aid=1509)

## 初始状态的属性名称相同导致同时加载两个页面视图

```xml
<view class="drawer_screen" bindtap="powerDrawer" data-statu="close" wx:if="{{showModalStatus1}}"></view>
```
其中，showModalStatus1与另一个名称为showModalStatus的属性值冲突。

## 操作按钮悬浮固定在底部

以收货地址为例，将添加地址按钮悬浮于最底部，这样再多的地址，也不会被遮挡而看不见。常见的有加入购物车按钮、结算按钮、收货列表添加地址按钮。

核心代码，如下:

```css
<button bindtap=add type=primary class=address-add>添加button>
/*添加地址按钮*/
.address-add {
    position: fixed;
    bottom: 0;
    width: 100%;
}
```
考虑到按钮自身占据46px高度，因此地址列表还需要加上如下样式:

```css
/*地址列表包装容器*/
.address-list {
    margin-bottom: 46px;
}
```

### 参考资料

[小程序操作按钮悬浮固定在底部](http://www.mntuku.cn/index.php/article/show/id-7141)

## 页面滚动范围设置

页面底部一个操作按钮，按钮上面是一个列表。为了在滚动列表时，底部按钮固定不动，需要设置页面的滚动范围。

.wxml文件:

```xml
<view class="page">
   <view class="useWrap" style="height:{{winHeight-60}}px;overflow:hidden;">
       <view  style="height:{{winHeight-60}}px;overflow:scroll;">
         <view data-index="{{index}}" class="mod t-sign-time" wx:for="{{classifyList}}" bindtap="selectedTap">
         <text data-index="{{index}}" class="content" bindtap="selectedTap">{{item}}</text>
         <image class="normal {{index == activeItemIndex ? 'selecteIcon':'unselecteIcon'}}" src="/image/selected.png" mode="center"></image>  
         </view> 
      </view>   
   </view>
</view>
<button class="sureBtn" bindtap="sureBtnClick">确定</button>

```

其中，

```xml
 <view class="useWrap" style="height:{{winHeight-60}}px;overflow:hidden;">
```
和

```xml
<view  style="height:{{winHeight-60}}px;overflow:scroll;">
```
即为设置的滚动范围，

```css
height:{{winHeight-60}}
```
是为了减去距离底部固定按钮的高度，在此范围进行滚动。

其中winHeight为在.js中获取到的当前屏幕的高度。

```js
onLoad: function (e) {
    var _this = this;
    //获取屏幕高度
    wx.getSystemInfo({
      success: function (res) {
        _this.setData({
          winWidth: res.windowWidth,
          winHeight: res.windowHeight
        })
      }
    })
},
```
## 动态的显示或隐藏控件(自定义单项选择样式)

显示控件调用.selecteIcon，隐藏控件调用.unselecteIcon。通过属性display来控制。

.wxss中的样式如下:

```css
/* 选中 */
.selecteIcon {
  display: block;
  width: 20px;
  height: 25px;
  margin-right: 30px;
}

/* 未选中 */
.unselecteIcon {
  display: none;
  width: 20px;
  height: 25px;
}
```

在初始状态下，需要给控件一个初始样式，

```css
/* 初始样式 */
.normal {
  width: 20px;
  height: 25px;
}
```

在点击时选中当前行，需要进行进行判断，通过数组的index确定当前行是否被选中，被选中即为activeItemIndex，.wxml代码如下，

```xml
<image class="normal {{index == activeItemIndex ? 'selecteIcon':'unselecteIcon'}}" src="/image/selected.png" mode="center"></image>  
```
这种样式写法，也可用于自定义单选样式。
### 参考资料

[动态的显示或隐藏控件](http://www.wxapp-union.com/portal.php?mod=view&aid=1261)

## 反向传值

有两个页面，这里定为页面A和页面B。点击页面A跳转至页面B，页面B中有一个确定按钮，需要在点击按钮时，将页面B中的某个数据传递给页面A。这里通过从页面路由栈中直接获取和操作目标Page对象。

这种方式，是通过调用小程序的API: getCurrentPages()，来获取当前页面路由栈的信息，这个路由栈中按照页面的路由顺序存放着相应的Page对象，我们可以很容易的获取到上一级页面的完整Page对象，从而使直接调用Page对象的属性和方法成为可能。

在页面B中的js文件中触发确定按钮的tap方法，并返回页面A,

```js
  /**
   * 点击确定按钮
   */
  sureBtnClick: function () {
    var that=this;
    var pages = getCurrentPages();
    var currPage = pages[pages.length - 1];   //当前页面
    var prevPage = pages[pages.length - 2];  //上一个页面
    //直接调用上一个页面的setData()方法，把数据存到上一个页面中去
    prevPage.setData({
      mydata: that.data.a,
    })

    wx.navigateBack({

    })
 }
```
页面中只需要在对应的.wxml文件中调用{{mydata}}即可获取页面B中回传的值。

### 参考资料

[微信小程序从子页面退回父页面时的数据传递](http://www.jianshu.com/p/aa8254b23847)

## bindtap事件传递

```js
data.currentTarget.dataset.index
```
与

```js
data.target.dataset.index
```
的区别:

target 触发事件的源组件，currentTarget 事件绑定的当前组件。如果你在父容器上绑定了事件并传参，当你点击父容器时，事件绑定的组件和触发事件的源组件是同一个元素，所以currentTarget 、target 都可以拿到参数，但是当你点击子元素时，target 就不是事件绑定的组件了，所以拿不到参数。
 
由于事件冒泡的机制，父容器上绑定的事件依然可以触发，所以currentTarget 依然可以拿到参数。

举个例子，

view标签 加 bindtap事件，用data-name传值，如果view中只有文字，点击整个view区域都可以接收到data-name的值，如果view里面加一个lable标签，那么点击lable包裹的区域，data-name取不到值。  

解决方法：把取值方式  由e.target.dataset.carrierName  修改为e.currentTarget.dataset.carrierName即可！

## 请求接口后使用that.setData无法为变量赋值

在页面的.js文件中的Page方法中为变量赋值，如下：

```js
Page({

  /**
   * 页面的初始数据
   */
  data: {
      medicineId:null,
      medicineType:null,

      commodity_name: '',
      commodity_code: '',
      medicinal_formId:'',
      specifcations:'',
      factory:'',
      direction:'',
      approval_num:'',
      categoryLabel:''   
  },

```
之后在请求接口成功后，给变量赋值:

```js
success: function(res) {
         console.log('GetMedicinalInfoUrl res = ',res.data);

         that.setData({
           commodity_name: res.data.data.commodity_name,
           commodity_code: res.data.data.commodity_code,
           medicinal_formId: res.data.data.medicinal_form_id,
           specifcations: res.data.data.specifcations,
           factory: res.data.data.factory,
           direction: res.data.data.direction,
           approval_num: res.data.data.approval_num,
           categoryLabel: res.data.category_label
         });
       },
```

发现日志一直会报"can not find variate commodity_name"，并且无法赋值。经过一番周折，试着将所有带下划线的变量名使用驼峰式命名，就可以正常赋值了。看来小程序不支持下划线命名。

## 在编辑完文件后忘记保存文件，导致运行时没有实际改变的问题。

## 切换不同Ip的调试环境

在调试接口时往往需要连接不同端口的IP，这里就需要设置一种可以灵活切换IP的方式。所有，在项目中引入了名为requestUrl.js的文件，在该文件中配置不同的Ip。

```js
var ApiRootUrl = 'http://192.168.10.11:8038'
module.exports = {
   RequestUrl1: ApiRootUrl + 'a/b',
   RequestUrl2: ApiRootUrl + 'c/d',
   RequestUrl3: ApiRootUrl + 'e/f',
   RequestUrl4: ApiRootUrl + 'g/h',
};
```
其中，变量ApiRootUrl为需要单独配置的用于切换的不同Ip。module.exports里为声明的，根据ApiRootUrl和访问路径拼接而成的url。

使用时需要在调用的.js文件中引入requestUrl.js文件:

```js
const requestUrl = require('../../config/requestUrl.js');
```

在请求接口的request方法的url参数中，调用常量requestUrl，

```js
wx.request({
   url: requestUrl. RequestUrl1,
})
```

## WAService.js:1 navigateTo:fail url not in app.json

因文件夹层级关系导致无法找到对应的文件。例如，用于切换不同IP的requestUrl.js文件:

这里的requestUrl.js文件是放在与pages目录平级的目录config下的。因此，当在pages目录下引入时，引入路径设置为:

```js
const requestUrl = require('../../config/requestUrl.js');
```

若此时pages目录下，需要访问requestUrl.js的页面又放在了另外一个目录下，则需要再原来基础之上，再引入一层，引入路径设置为:

```js
const requestUrl = require('../../../config/requestUrl.js');
```

### 参考资料

[app.json的url问题](http://www.wxapp-union.com/forum.php?mod=viewthread&tid=3611)

## JS判断字符串是否包含某个字符

```js
var tempString = "text";
if (tempString.indexOf("xt") >= 0) {
    // 包含某个字符
}
```
若包含则返回大于等于0的整数值，若不包含则返回-1。

## App()函数

App()函数用来注册一个小程序。接受一个object参数，其指定小程序的生命周期函数等。

生命周期函数:

> onLaunch 监听小程序初始化，当小程序初始化完成时，会触发onLaunch(全局只触发一次)

> onShow 监听小程序显示，当小程序启动，或从后台进入前台显示，会触发onShow
 
> onHide 监听小程序隐藏，当小程序从前台进入后台，会触发onHide

需要注意App()函数与Page()函数的区别。在app.js里使用onShow函数会导致一系列的问题。例如，想在程序每次打开时，调用接口校验用户登录状态是否过期。如果放在app.js里的onLaunch函数里在全局只会触发一次，如果放在app.js里的onShow函数里，则当进行页面切换操作时，app.js里的onShow函数会不定时的触发或造成其他页面在进行跳转操作时，多次跳转。例如，调用wx.scanCode时，在扫码页面返回上一个页面时，会跳转两次。

## that,this

每次读取页面的值时，都忘记使用that或this读取在page的data:{}中声明的变量。

```js
var that = this;
```

## 小程序页面路径只能是五层

> 为了不让用户在使用小程序时造成困扰，我们规定页面路径只能是五层，请尽量避免多层级的交互方式。

## 关于"undefined"的处理

遇到需要判断某个变量是否被声明并赋值，若未声明，则console该变量后，在控制台会输出"undefined"，那么在判断条件里判断变量是否存在就没有什么意义了。

```js
  var exp = that.data.apropertiesTextVal;  if (typeof (exp) == "undefined"){    that.setData({      showModalStatus6: false,      tag: true,  })
```

需要使用typeof转化变量。

## 避免快速点击，多次触发bindtap事件，造成打开多个相同页面

如果点击buton触发bindtap跳转事件，可以通过设置button的disabled属性值:

> disabled(是否禁用)为bool值，默认值为false(不禁用)

另一种情况，点击列表某行时跳转至某个页面。当快速连续点击时会打开多个相同页面。此时，bindtap事件放置在View标签上。而view没有disabled属性，所以需要定义一个bool变量，用来替代disabled。

```js
Page({
  data: {
    isTapClick: true  // 避免快速点击，造成多次跳转同一页面的bool变量
  }，
  
  onShow: function() {
     var that = this;
     that.setData({
        isTapClick: true
     })
  },
  
  listNavigateTo: function(res) {
     var that = this;
     if (that.data.isTapClick) {
        that.setData({
           isTapClick: false
        })
        wx.navigateTo({
           url: '../pages/pages'
        })
     }
  }
})

```

判断isTapClick是否为true，为true则执行跳转，并且每次执行时都需要将isTapClick变量置为false(正是这句设置避免了重复进行页面跳转)。

每次都需要在onShow函数里再将isTapClick置为true，以便再次展示改页面时仍能点击列表。



