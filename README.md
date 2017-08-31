# WXAppPits

# 微信小程序填坑记录

## 在wxml文件中使用text标签时内容不能与标题对齐的问题

```objetive-c
  <view class="mod t-sign-time">
          <text>商品条码</text>
          <text class="value">
          67882932222222222</text>
  </view>
```
微信编译器识别到text中含有空行导致。修改如下:

```objetive-c
  <view class="mod t-sign-time">
          <text>商品条码</text>
          <text class="value">67882932222222222r33ddfdfdfdf</text>
 </view>
```

## 文本无法居中

当设置display为inline-block时，遇到文本内容设置text-align: center始终无法居中的问题。虽然设置display: flex可以解决文本居中的问题，但整体View上的多个控件布局会乱。最后设置，

```
line-height: 31px;
```
解决问题。(其中，31px为该视图控件的高度。) 通过设置line-height确定了显示文本内容的行高。

## justify-content属性

当一个视图上有多个控件，且这些控件处在一行时。使用justify-content可以很方便的控制这些控件的对齐方式，间距等样式。

.wxml

```
 <view class="drawer_title">
     <view class="btn_cancel" bindtap="powerDrawer" data-statu="close">取消</view>
     <text>剂型选择</text>
     <view class="btn_sure" bindtap="powerDrawer" data-statu="close">确定</view>
  </view>  
```

.wxss

```
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

```
  <view class="drawer_content">
     <block wx:for="{{['注射剂1', '注射剂2', '注射剂3', '注射剂4', '注射剂', '注射剂', '注射剂','注射剂', '注射剂', '注射剂', '注射剂', '注射剂', '注射剂', '注射剂']}}">
        <text data-index="{{index}}" class="view-list {{index ==activeItemIndex ? 'active':''}}" bindtap="formTapClick">{{item}}</text>
     </block>
  </view>  

```
其中，默认未选中的样式为view-list，需要在class中默认写上。即，

```
class="view-list {{index ==activeItemIndex ? 'active':''}}"
```
active为选中时改变背景需要调用的样式。

### 参考资料

[小程序中的循环列表，在点击时改变当前项的背景颜色](http://www.wxapp-union.com/portal.php?mod=view&aid=1509)

## 初始状态的属性名称相同导致同时加载两个页面视图

```
<view class="drawer_screen" bindtap="powerDrawer" data-statu="close" wx:if="{{showModalStatus1}}"></view>
```
其中，showModalStatus1与另一个名称为showModalStatus的属性值冲突。

## 操作按钮悬浮固定在底部

以收货地址为例，将添加地址按钮悬浮于最底部，这样再多的地址，也不会被遮挡而看不见。常见的有加入购物车按钮、结算按钮、收货列表添加地址按钮。

核心代码，如下:

```
<button bindtap=add type=primary class=address-add>添加button>
/*添加地址按钮*/
.address-add {
    position: fixed;
    bottom: 0;
    width: 100%;
}
```
考虑到按钮自身占据46px高度，因此地址列表还需要加上如下样式:

```
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

```
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

```
 <view class="useWrap" style="height:{{winHeight-60}}px;overflow:hidden;">
```
和

```
<view  style="height:{{winHeight-60}}px;overflow:scroll;">
```
即为设置的滚动范围，

```
height:{{winHeight-60}}
```
是为了减去距离底部固定按钮的高度，在此范围进行滚动。

其中winHeight为在.js中获取到的当前屏幕的高度。
```
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


