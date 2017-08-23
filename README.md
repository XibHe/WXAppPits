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
微信编译器识别到text前的空格的问题。修改如下:

```objetive-c
  <view class="mod t-sign-time">
          <text>商品条码</text>
          <text class="value">67882932222222222r33ddfdfdfdf</text>
 </view>
```

