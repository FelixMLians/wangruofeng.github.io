---
layout: post
title: "iOS Animation Swift 版"
date: 2016-01-16 19:34:06 +0800
comments: true
tags: [iOS, Animation, Swift动画, Octopress]
keywords: octopress, iOS, Animation, Swift, Objective-c, CoreAnimation
categories: iOS Animation
description: iOS Animation Swift 版
---

![coreAnimation](http://ww2.sinaimg.cn/large/64124373gw1f01qjo2k00j21g00ejjvd.jpg)


### 简介
此文主要记录我学习iOS的动画相关内容用`Swift`语言，记录下来以供参考，不定时更新。

### 目录结构

* Section I: View 动画
    * View animation
    * 弹簧动画 - Springs
    * 转场动画 - Transitions
    * 关键帧动画 - Keyframe
* Section II: Auto Layout 动画
* Section III: Layer 动画
* Section IV: 3D 动画
* Section V: 未来类型的动画
* Section VI: View Controller 动画
* Section VII：第三方动画库

#### Section I: View 动画

##### 1.View animation

简介：view 的动画主要是通过UIView的类方法创建，动画内容一般
放在block里面，可以镶嵌使用，构成链式动画，主要有3个方法,三个访法只需会参数最多的那个就行，函数名`animateWithDuration(_:delay:options:animations:completion:)`，其他的使用起来都类似。

```swift
class func animateWithDuration(_ duration: NSTimeInterval, animations animations: () -> Void)
class func animateWithDuration(_ duration: NSTimeInterval, animations animations: () -> Void, completion completion: ((Bool) -> Void)?)
class func animateWithDuration(_ duration: NSTimeInterval, delay delay: NSTimeInterval, options options: UIViewAnimationOptions, animations animations: () -> Void, completion completion: ((Bool) -> Void)?)
```

使用：
```swift
UIView.animateWithDuration(0.5, delay: 0, options: [], animations: {
    //do something
  }, completion: {_ in
    //do something
})
```

> 说明：`options`，动画选项，这个运行你设置一个动画选项集合，传`[]`表示不需要选项，单个时可以省略放括号例如`.Repeat`,多个用逗号连接`[.Repeat, .CurveEaseInOut]`

> `completion`闭包需要一个参数，不需要使用参数可以用`_`表示，不需要实现可以直接传`nil`

或者像下面这样写

```swift
UIView.animateWithDuration(0.5, delay: 0, options: [], animations: { () -> Void in
  //do something
  }) { (_) -> Void in
  //do something
}
```

##### 2.弹簧动画 - Springs

简介：弹簧动画是iOS 7.0新增的API，函数名`animateWithDuration(_:delay:usingSpringWithDamping:initialSpringVelocity:opti ons:animations:completion:)`，

```swift
class func animateWithDuration(_ duration: NSTimeInterval, delay delay: NSTimeInterval, usingSpringWithDamping dampingRatio: CGFloat, initialSpringVelocity velocity: CGFloat, options options: UIViewAnimationOptions, animations animations: () -> Void, completion completion: ((Bool) -> Void)?)
```

使用：
```swift
UIView.animateWithDuration(0.5, delay: 0.5, usingSpringWithDamping: 0.5, initialSpringVelocity: 0.0, options: [], animations: {
// do something
}, completion: nil)
```

> 说明：`usingSpringWithDamping`:设置弹簧的阻尼，范围（0.0-1.0），越接近0.0弹簧越有弹性，越接近1.0，效果越僵硬，你可以把当成弹簧的刚度来理解，
> `initialSpringVelocity`:设置弹簧初始化的速度，设置1.0表示用1秒在动画跨度上完成整个动画距离，值越大或者越小会导致动画有相应的大的或者较小的速度，可以当成弹簧的初始动量来理解。

##### 3.转场动画 - Transitions

简介：前两种创建的动画是基于可动画的接口例如，position，alpha，frame等等，Transitions是专门处理添加或者移除一个view的动画,系统有2个标准函数，一个是对单个view的处理，一个是处理2个view替换。

```swift
class func transitionWithView(_ view: UIView, duration duration: NSTimeInterval, options options: UIViewAnimationOptions, animations animations: (() -> Void)?, completion completion: ((Bool) -> Void)?)
class func transitionFromView(_ fromView: UIView, toView toView: UIView, duration duration: NSTimeInterval, options options: UIViewAnimationOptions, completion completion: ((Bool) -> Void)?)
```

使用：
```swift
//add the new view via transition
UIView.transitionWithView(animationContainerView!, duration: 0.33,
options: [.CurveEaseOut, .TransitionFlipFromBottom], animations: {
self.animationContainerView!.addSubview(newView) }, completion: nil)

//remove the view via transition
UIView.transitionWithView(animationContainerView!, duration: 0.33,
options: [.CurveEaseOut, .TransitionFlipFromBottom], animations: {
self.newView.removeFromSuperview() }, completion: nil)

//hide the view via transition
UIView.transitionWithView(self.newView, duration: 0.33, options: [.CurveEaseOut, .TransitionFlipFromBottom], animations: {
self.newView.hidden = true }, completion: nil)

//replace via transition
UIView.transitionFromView(self.oldView!, toView: self.newView!, duration: 0.33, options: [.TransitionFlipFromTop],
completion: nil)
```

##### 4.关键帧动画 - Keyframe

简介：关键帧顾名思义就是设定关键的一些帧然后系统会根据一套算法计算中间的其他帧，这样改变的可动画属性时看看起来更加流畅自然，主要有两个函数,一个创建关键帧动画，一个设置具体每帧内容

```swift
class func animateKeyframesWithDuration(_ duration: NSTimeInterval, delay delay: NSTimeInterval, options options: UIViewKeyframeAnimationOptions, animations animations: () -> Void, completion completion: ((Bool) -> Void)?)
+ (void)addKeyframeWithRelativeStartTime:(double)frameStartTime relativeDuration:(double)frameDuration animations:(void (^)(void))animations
```

例如：
```swift
func planeDepart() {

  UIView.animateKeyframesWithDuration(1.5, delay: 0.0, options: [], animations: {
    //add keyframes

    UIView.addKeyframeWithRelativeStartTime(0.0, relativeDuration: 0.25, animations: {
      self.planeImage.center.x += 100.0
      self.planeImage.center.y -= 10.0
      })

      UIView.addKeyframeWithRelativeStartTime(0.1, relativeDuration: 0.4) {
        self.planeImage.transform = CGAffineTransformMakeRotation(CGFloat(-M_PI_4/2))
      }

      UIView.addKeyframeWithRelativeStartTime(0.25, relativeDuration: 0.25) {
        self.planeImage.center.x += 100.0
        self.planeImage.center.y -= 60.0
        self.planeImage.alpha = 0.0
      }

      UIView.addKeyframeWithRelativeStartTime(0.51, relativeDuration: 0.01) {
        self.planeImage.transform = CGAffineTransformIdentity
        self.planeImage.center = CGPoint(x: 0.0, y: originalCenter.y)
      }

      UIView.addKeyframeWithRelativeStartTime(0.55, relativeDuration: 0.45) {
        self.planeImage.alpha = 1.0
        self.planeImage.center = originalCenter
      }

      }, completion: nil)
}
```
> 说明：这是设计一个飞机沿一条路径起飞然后再归位的一段动画，`addKeyframeWithRelativeStartTime(_:relativeDuration:animations:)`,第一个参数相对开始时间，是相对于动画持续时间的百分比，例如0.1就是10%，0.25就是25%，如果整个动画持续2秒，0.1就是在2*0.1=0.2秒的时候开始，后面的一个参数是相对持续时间，范围更第一参数类似也(0.0-1.0),只从相对开始时间起，往后推移的相对时间。


备注：欢迎转载，但请一定注明出处！ <http://blog.wangruofeng007.com>
