---
layout: post
title: "TableView性能优化"
date: 2016-01-18 22:00:04 +0800
comments: true
tags: [iOS, Blog, TableView, Octopress]
keywords: octopress, iOS, TableView性能优化, Objective-c ,Swift
categories: Efficiency Favorite iOS
description: TableView性能优化
---


> 此文整理一下TableView优化相关的方案和思路。
 
#### TableView为什么会卡？

主要由以下原因：

* `cellForRowAtIndexPath:`方法中处理了过多业务
* tableviewCell的subview层级太复杂，做了大量透明处理
* cell的height动态变化时计算方式不对

#### 优化核心思想：`UITableViewCell`重用机制
 
简单的理解就是：UITableView只会创建一屏幕（或一屏幕多一点）的UITableViewCell，其他都是从中取出来重用的。每当Cell滑出屏幕时，就会放入到一个集合（或数组）中（这里就相当于一个重用池），当要显示某一位置的Cell时，会先去集合（或数组）中取，如果有，就直接拿来显示；如果没有，才会创建。这样做的好处可想而知，极大的减少了内存的开销。



#### Tips：

1. 提前计算并缓存好高度（布局），因为`heightForRowAtIndexPath:`是调用最频繁的方法；
2. 异步绘制,遇到复杂界面,参考`Facebook`的`AsyncDisplayKit`和[YYAsyncLayer](https://github.com/ibireme/YYAsyncLayer)异步绘制框架；
3. 缓存图片（`SDWebImage`），提前处理好`UIImageView`图片的尺寸按需加载而不是加载原图；
4. 计算等耗时操作异步处理，处理完再回主线程更新UI；
5. 图文混排不定高度采用`CoreText`排版，缓存Cell高度参考[YYKit](https://github.com/ibireme/YYKit)；
6. 实现Cell的`drawRect:`方法直接绘制，减少`UIView`，`UIImageView`，`UILabel`等容器的使用。

#### Bonus：

1. 正确使用reuseIdentifier来重用Cell；
2. 尽量少用或不用透明图层或View；
3. 如果Cell内现实的内容来自web，使用异步加载，缓存请求结果；
4. 减少subviews的数量在`heightForRowAtIndexPath:`中尽量不使用`cellForRowAtIndexPath:`，如果你需要用到它，只用一次然后缓存结果；
5. 尽量少用addView给Cell动态添加View，可以初始化时就添加，然后通过hide来控制是否显示；
6. 固定高度不要实现`heightForRowAtIndexPath:`方法。

可以通过实现以下方法，可以减少高度计算次数

```objective-c
@property (nonatomic) CGFloat rowHeight;             // will return the default value if unset
@property (nonatomic) CGFloat sectionHeaderHeight;   // will return the default value if unset
@property (nonatomic) CGFloat sectionFooterHeight;   // will return the default value if unset
@property (nonatomic) CGFloat estimatedRowHeight NS_AVAILABLE_IOS(7_0); // default is 0, which means there is no estimate
@property (nonatomic) CGFloat estimatedSectionHeaderHeight NS_AVAILABLE_IOS(7_0); // default is 0, which means there is no estimate
@property (nonatomic) CGFloat estimatedSectionFooterHeight NS_AVAILABLE_IOS(7_0); // default is 0, which means there is no estimate
```


参考资料：

* [code-uitableviewcell-optimizations-part-1](http://ealui.com/index.php/blog/view/code-uitableviewcell-optimizations)
* [code-uitableviewcell-optimization-part-2](http://ealui.com/blog/view/code-uitableviewcell-optimization-part-2)
* [Perfect smooth scrolling in UITableViews](https://medium.com/ios-os-x-development/perfect-smooth-scrolling-in-uitableviews-fd609d5275a5#.b176bxlm3)
*  [优化UITableViewCell高度计算的那些事](http://blog.sunnyxx.com/2015/05/17/cell-height-calculation/)
*  [详细整理：UITableView优化技巧](http://www.cocoachina.com/ios/20150602/11968.html)
*  [UITableview性能优化总结](http://www.cnblogs.com/wengzilin/p/4288027.html)
*  [AsyncDisplayKit 教程：达到 60 FPS 的滚动帧率](https://github.com/nixzhu/dev-blog/blob/master/2014-11-22-asyncdisplaykit-tutorial-achieving-60-fps-scrolling.md)
*  [AsyncDisplayKit](https://github.com/facebook/AsyncDisplayKit) 
*  [YYAsyncLayer](https://github.com/ibireme/YYAsyncLayer)
