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
