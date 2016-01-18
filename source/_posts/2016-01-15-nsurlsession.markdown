---
layout: post
title: "NSURLSession 教程"
date: 2016-01-15 18:15:01 +0800
comments: true
tags: [iOS, Networking, NSURLSession, Octopress]
keywords: Octopress, iOS, POST, NSURLSession, Swift, Objective-c, Networking
categories: iOS Networking
description: NSURLSession 教程
---

本文翻译自 http://www.raywenderlich.com/51127/nsurlsession-tutorial

原作者： Charlie Fulton

译者：[@oneruofeng](https://twitter.com/oneruofeng)


注意：这是一篇来自我们作为即将发布[ iOS 7 Feast](http://www.raywenderlich.com/?p=49762)的一部分[iOS 7 by Tutorials](http://www.raywenderlich.com/?page_id=48020)的简短版本的章节。我们希望你们喜欢。

每一个新的iOS版本发布都会包含一些极好的新的网络APIs，iOS7也不例外，在iOS7中，Apple引入了`NSURLSession`,这是为了取代`NSURLConnection`作为偏好的网络请求的一系列类。

在这个`NSURLSession`的教程中，你将了解到这个新类究竟是什么，为什么你要使用它以及你怎样使用它，它和以前的类库比较而言怎样，最后最重要的是：获得一个整合到一个真正的App的实践。

请注意：这个课程是假设你熟悉基本的网络概念。假如网络对你来说完全是新的，你仍然可以跟我一起一步一步的学习，但是可能有些你不熟悉的概念需要需要自己查询在这个过程中。

#### 为什么使用`NSURLSession`

为什么你要使用`NSURLSession`? 饿，因为它可以带给你一些好处和优势相比以前的：

* **后台上传和下载** ： 当你创建`NSURLSession`的时候你只需配置一个选项即可，你便可以进行所有的后台网络任务。这将有对你的电池寿命有利，它支持`UIKit`多任务并且当切换线程的时候使用相同的代理模型。
* **使你的网络操作可以暂停和恢复** ：你稍后将会看到，任何使用`NSURLSession`API的网络任务都可以被暂停，停止，重新开始。而没有使用`NSOperation`子类的必要。
* **可配置的容器**：对于放进里面的请求而言每一个`NSURLSession`都是可配置的。例如，假如你需要设置一个HTTP header选项，你只需要设置一次然后每个在session中的请求就都会有相同的配置了。
* **可子类化和私密存储**： `NSURLSession`是可子类化的并且你可以配置一个session用来作为私密存储在某个会话中。这允许你拥有私密存储对象在全局状态下。
* **优化的授权处理机制**：授权被完成基于某个特定的连接。当使用`NSURLConnection`的时候假如发生了一处授权改变，这个改变将返回一个随意的请求，你不能确定你具体得到的那个。使用`NSURLSession`的话，代理回来处理授权。
* **丰富的代理模型**：`NSURLConnection`有些基于block的同步方法，然而代理就不能使用它们了。当一个请求建立了无论它是成功还是失败，哪怕需要授权。使用`NSURLSession`就就可以混合接入，使用基于block的异步方法同时也可以设置代理来处理授权。
* **通过文件系统上传和下载**：苹果鼓励把（文件内容）数据跟(URL和一些设置)元数据分开。

#### `NSURLSession` vs `NSURLConnection`

"哇哦，`NSURLSession`听起来很复杂呀！",你可能这么想。"我可能还是继续使用我的老朋友`NSURLConnection`。"

别担心--使用`NSURLSession`使用起来其实和它的前辈`NSURLConnection`一样简单对于简单的任务来说。例如，让我来看一个在获取伦敦最新的天气的一个简单网络请求，通过它来获取JSON数据的例子。

假设你用这个`NSString`来构造这个`NSURL`:

```objective-c
NSString *londonWeatherUrl = @"http://api.openweathermap.org/data/2.5/weather?q=London,uk";
```

这里是第一步你使用`NSURLConnection`来调用：

```objective-c
NSURLRequest *request = [NSURLRequest requestWithURL:
[NSURL URLWithString:londonWeatherUrl]];

[NSURLConnection sendAsynchronousRequest:request
   queue:[NSOperationQueue mainQueue]
   completionHandler:^(NSURLResponse *response,
                       NSData *data,
                       NSError *connectionError) {
      // handle response
}];
```

现在让我们来使用`NSURLSession`。注意这是使用`NSURLSession`的最简单的方式来快速构造一个请求。在后面的课程你将看到怎样配置session和设置其他特征比如像代理。

```objective-c
NSURLSession *session = [NSURLSession sharedSession];
[[session dataTaskWithURL:[NSURL URLWithString:londonWeatherUrl]
          completionHandler:^(NSData *data,
                              NSURLResponse *response,
                              NSError *error) {
            // handle response

  }] resume];
```

注意你并不需要指定它运行在那个队列中。除了你特别指定，这个调用将会在后台线程。你可能很难注意到这两者之间有什么不同，它就是故意这样的。Apple提到打算使用`dataTaskWithURL`来取代在`NSURLConnection`中的`sendAsynchronousRequest`。

所有从根本上来讲--对于简单的任务使用`NSURLSession`就和使用`NSURLConnection`一样简单，并且它还有一些列额外的定制功能当你需要它的时候。

#### `NSURLSession` vs `AFNetworking`

不提到`AFNetworking`框架就谈不上谈论网络编程。这个事在`iOS/OS X`上最流行的框架，有杰出的`Mattt Thompson`创建。

> 注意：想了解更多关于`AFNetworking`,请检出在<https://github.com/AFNetworking/AFNetworking>的github页面。我们也有一个关于它的课程：<http://www.raywenderlich.com/30445/afnetworking-crash-course>

下面是的使用`AFNetworking` 1.x版本处理相同的数据任务的代码：

```objective-c
NSURLRequest *request = [NSURLRequest requestWithURL:
                         [NSURL URLWithString:londonWeatherUrl]];

AFJSONRequestOperation *operation =
[AFJSONRequestOperation JSONRequestOperationWithRequest:request
    success:^(NSURLRequest  *request,
              NSHTTPURLResponse  *response,
              id JSON) {
    // handle response
} failure:nil];
[operation start];
```

使用`AFNetworking`的一个好处是处理响应的数据的数据根据类型被归类。使用`AFJSONRequestOperation`(或者诸如`XML`和plist相似的类),成功block已经被解析根据响应并且为你返回你想要的数据。使用`NSURLSession`你将收到一个`NSData`对象在`completion handler`,所有你只需要把`NSData`转换成`JOSN`或者其他形式。

> 注意：你能够很方便的把数据从`NSData`转换成`JSON`使用在iOS 5引入的`NSJSONSerialization`这个类。如果你想了解更多，请查看23章的iOS 5 教程，“Working with JSON”。

你或许想知道你是应该使用`AFNetworking`还是仅仅是继续使用`NSURLSession`。

就个人而言，我认为对于简单的需求最好还是继续使用`NSURLSession`--这样可以避免不必要的引入一个第三方库在你的工程中。另外，使用新的代理，配置，和基于很多添加到`AFNetworking`中的的“遗失特征”现在都被包括了的API的任务。

然而，假如你想使用一些在`AFNetworking`中2.0版本的新特性，诸如序列化和将来对
`UIKit`的整合（添加到`UIIImageView`分类中），然后这样很难争辩不使用它！

> 注意：在`AFNetworking`2.0分支中，它们已经转换到使用`NSURLSession`。更多信息看这篇帖子：<https://github.com/AFNetworking/AFNetworking/wiki/AFNetworking-2.0-Migration-Guide>

#### 介绍 Byte Club

在这篇`NSURLSession`教程中，你将探索这个新的API通过构建一篇日记好图片分享app基于` Dropbox Core API`,因为是顶级机密组织因此姑且命名它`Byte Club`。

考虑到这个课程是你接受到`Byte Club`的官方邀请！什么是你可能会问到的关于`Byte Club`的第一条规则？没人谈论`Byte Club`--除了那些足够炫酷的人将会阅读这个教程。并且那些Android用户完全不知道；他们被他们俩的生活劫持了。 ：]

开始构建app迎战下一个章节，将充当被`Byte Club`组织邀请。

主要到这个教程是假设你对基本的网络有基本的了解在先前的版本iOS。它非常有用假如你已经使用过诸如`NSURLConnection`或者`NSURLSession`在过去。假如在iOS方面你是网络方面的新手，在继续这个课程之前你应该查询我们的iOS学徒系列作为最初的开发者。

#### 现在开始吧

`Byte Club`是iOS 开发者专有的组织，一起来加入挑战你的编程吧。由于每个成员都是远程工作，在这个挑战中有一个是跨越世界，成员通过分享他们“战场”的全景照片也能找到它的乐趣。

例如，下面是Ray的办公场所的全景照片：
![Ray's office setup](http://ww4.sinaimg.cn/mw690/64124373gw1ezzf1ygxi8j20go03njs2.jpg)

> 注意：你可能想创建你自己办公室的全景照片--它很有趣，在这个课程的后续中我们将会处理。

> 在 iOS 7中，你可以通过打开相机选择一个叫`Pano`（全景）的标签照一张全景照片。

> 加入你喜欢那张，把它设置成你锁屏界面的墙纸通过打开`设置`让后选择`墙纸`  \选择墙纸 \我的全景照片。

当然-`Byte Club `有它自己的app，我们来见证奇迹。你可以和其他成员使用app来完成编程挑战或者分享全景照片。在幕后，这是通过网络实现-明确的说，就是通过`Dropbox API`来分享文件。

#### 开始的工程概述

首先，下载[教程开始的工程](http://cdn1.raywenderlich.com/downloads/ByteClub_Starter.zip)。

开始的工程包含了为你预先准备好的UI，所以你只需把精力集中在这个教程中app的网络部分。开始的工程也包含一些处理`Dropbox`授权的代码,在后面你将学到更多。

在Xcode中打开工程让后在你设备或者模拟器上运行，你应该看到像下面这样：

![networking2](http://ww1.sinaimg.cn/mw690/64124373gw1ezzf1vk3t1j205b09edg2.jpg)

然而你还并不能登录它-你不得不先配置app，你将做一点。

下一步打开` Main.storyboard `纵览一下整个app的设计：

![networking2](http://ww2.sinaimg.cn/mw690/64124373gw1ezzf1x56zfj20go0a9q3e.jpg)

这是一个最基本的使用2个标签的的TabBarController app：一个是为了挑战编程，另
一个是为了放全景照片。这里也有预先让用户登录到app中的一步。你将要配置登录在你创建完`Dropbox`平台下的App后。

感到很轻松浏览一下App剩余的部分并且找到到目前为止相似的地方。你将会注意到除了授权组件，这里没有检索挑战编程或者全景照片的网络代码-那就是你的工作！

#### 创建一个新的`Dropbox`平台App

为了开始你的新的`Dropbox` App,打开 Dropbox App 位于<https://www.dropbox.com/developers/apps>的控制台

用你的`Dropbox`账号登录，假如没有，没问题：马上创建一个免费的Dropbox账号。假如这是你第一次使用Dropbox的API，你需要通知Dropbox的条款和条件。

经过这个法定的材料以后就是上路了，选择创建App选项。将呈现给你一系列问题-提供下面的答案

* What type of app do you want to create?
    * Choose: **Dropbox API app**
* What type of data does your app need to store on Dropbox?
    * Choose: **Files and Datastore**
* Can your app be limited to its own, private folder?
    * Choose: **No – My App needs access to files already on Dropbox**
* What type of files does your app need access to?
    * Choose: **All File Types**

最终，为你的App准备一个名字，选择什么并没有关系只有它是唯一的。假如你选择了一个别人已经在使用的名字`Dropbox `将会告诉你。你的屏幕应该看起来像下面这样：

![networking3](http://ww2.sinaimg.cn/mw690/64124373gw1ezzf38fs1nj20go0epdhp.jpg)    

点击`Create App`，你将开始上路了！

下一个屏幕你将看到显示到屏幕中的包含 **App key** 和 **App secret** :

![networking5](http://ww3.sinaimg.cn/mw690/64124373gw1ezzfuk4ucmj208z08rglt.jpg)

先不要关闭这个屏幕，你将需要需要下一步的`App Key`和`App Secret`。

打开`Dropbox.m`文件找到下面这些行：

```objective-c
#warning INSERT YOUR OWN API KEY and SECRET HERE
static NSString *apiKey = @"YOUR_KEY";
static NSString *appSecret = @"YOUR_SECRET";
```

填写你的app key 和 secret，让后删除 #warning line，现在你可以关闭`Dropbox` Web App 页面。

下面，创建一个文件夹在你Dropbox主文件下的根目录给它命一个你想要的名字。假如你把这个文件夹个和其他的Dropbox用户分享，发送他们在构建 Byte Club App的时候，他们将能够创建笔记并且能够上传所有人都能看得见的照片。

在Dropbox.m中找打下面这些行：

```objective-c
#warning THIS FOLDER MUST BE CREATED AT THE TOP LEVEL OF YOUR DROPBOX FOLDER, you can then share this folder with others
NSString * const appFolder = @"byteclub";
```

改变字符串的值，设置成你创建的Dropbox文件夹的名字，让后删除 #warning pragma.

为了把这个app分发给其他用户，给他们接入`access tokens`,你将需要为你的Dropbox 平台 App打开`Enable additional users`设置。

去在<https://www.dropbox.com/developers/apps>Dropbox app的控制台。点击你app 名称。然后点击`Enable Additional Users`按钮。将出现一个状态对话框表明你已经增加了你的用户限制。点击Okay关闭对话框。你的App 页面将像下面这样显示：

![networking5](http://ww3.sinaimg.cn/mw690/64124373gw1ezzg9htvs2j20go056jrj.jpg)

> 注意：你可能注意到当你正在开发你的app的时候，你可以接入多达100个用户。当你准备发布app销售的时候，你必须申请生产状态，你可以通过点击`Apply for production`按钮来发送给`Dropbox`一些额外的信息。

> Dropbox 将随后审核你的App 来确保它遵守指南，假如所有的一切进行得顺利的话，你将打开你的app的 API接入无线的用户。

#### Dropbox 授权: 概览

假如你曾经使用过第三方`twitter`客服端app，像`TweetBot`,你将会熟悉`OAuth`授权处理步骤从一个用户的角度。`OAuth`授权接入过程对你app来说是完全一样的。

构建运行你的app，按照步骤登录。你将看到一个有2个标签的空白屏幕，一个是Notes，一个是PanoPhotos，如下图显示：

![networking7](http://ww3.sinaimg.cn/mw690/64124373gw1ezzglpqm1aj209f0fa0t7.jpg)

`OAuth`授权发生在3和高级的步骤：

1. 获取用来处理剩下的授权一个OAuth请求 token。这是请求token。
2. 一个web 页面被呈现到用户面前通过他们的web浏览器。没有这一步的用户授权，对你的应用想获取一个第三步中的接入token几乎不可能。
3. 在第二步完成后，应用调用web服务来交换临时请求token（从第一步中的）为了一个将存储在app里面的持久接入token。

> 注意：为了保证这个教程的简洁，我们不打算进行更详细的讲解关于这里Dropbox授权工作。然而，假如你想了解更多点击整个教程的完全版本，它是[iOS 7 by Tutorials.](http://www.raywenderlich.com/?page_id=48020)的一部分。

#### NSURLSession 的一系列类

Apple已经把`NSURLSession`描述成一个新类和一系列旧类的组合。这些新的工具是为了处理 上传，下载，处理授权已经处理在HTTP协议里面的任何事情。

![networking12](http://ww3.sinaimg.cn/mw690/64124373gw1ezzgzutn43j20go0dwgn3.jpg)

一个`NSURLSession`用一个带可选代理的`NSURLSessionConfiguration`构造。在你创建会话以后，你应该能够满足你的网络需要通过创建`NSURLSessionTask`的任务。

#### NSURLSessionConfiguration

这里有三种方式创建`NSURLSessionConfiguration`:

* **defaultSessionConfiguration** - 创建一个使用全局缓存，cookie的配置对象和凭证存储的对象。这个配置会使你的会话最像`NSURLConnection`。
* **ephemeralSessionConfiguration** - 这个配置是用来作为‘私有的’会话并且不会持久化存储缓存，cookie，或者信用存储对象。
* **backgroundSessionConfiguration** - 当你想要从远程推送或者当app被暂时挂起时进行网络业务使用这个这个配置。参考17章和18章在[ iOS 7 by Tutorials](http://www.raywenderlich.com/?page_id=48020),`Beginning and Intermediate Multitasking`,有更详细的讲解。

一旦你创建一个`NSURLSessionConfiguration`对象，你就可以在它上面设置各种接口像这样：

```objective-c
NSURLSessionConfiguration *sessionConfig =
[NSURLSessionConfiguration defaultSessionConfiguration];

// 1
sessionConfig.allowsCellularAccess = NO;

// 2
[sessionConfig setHTTPAdditionalHeaders:
          @{@"Accept": @"application/json"}];

// 3
sessionConfig.timeoutIntervalForRequest = 30.0;
sessionConfig.timeoutIntervalForResource = 60.0;
sessionConfig.HTTPMaximumConnectionsPerHost = 1;
```

1. 你限制了网络操作只有wifi才能进行。
2. 这将设置所有的请求只接受 JSON类型的响应。
3. 这些接口将配置资源或者请求超时时间。你也可以限制你的app对你的主机只能有一个网络连接。

这些仅仅是你能配置的一些东西，确保检查所有列表的文档。

#### NSURLSession

`NSURLSession`被设计成替代`NSURLConnection`的API。Sessions做了他们的工作通过他们的部下，也就是非常出名的`NSURLSessionTask`对象。使用`NSURLSession`你能够创建任务使用基于block的便利方法，设置一个代理，或者同时两者。例如，假如你想要下载一张
图片（ *challenge hint *）,你就需要创建一个`NSURLSessionDownloadTask`。

第一步，你需要创建(会话)session。 这里有一个例子：

```objective-c
// 1
NSString *imageUrl =
@"http://www.raywenderlich.com/images/store/iOS7_PDFonly_280@2x_authorTBA.png";

// 2
NSURLSessionConfiguration *sessionConfig =
  [NSURLSessionConfiguration defaultSessionConfiguration];

// 3
NSURLSession *session =
  [NSURLSession sessionWithConfiguration:sessionConfig
                                delegate:self
                           delegateQueue:nil];
```

Ok,这个仅仅是你目前所看到的一点点不同。让我们一步步重温。

1. 用这个代码片段我们将一样进行下载在两个任务中。
2. 你总是以创建`NSURLConfiguration`开始。
3. 这里创建一个会话使用现在的类作为代理。

在你创建会话后，你可以通过创建一个带一个`completion handler`的任务下载这张图片，想下面这样:

```objective-c
// 1
NSURLSessionDownloadTask *getImageTask =
[session downloadTaskWithURL:[NSURL URLWithString:imageUrl]

    completionHandler:^(NSURL *location,
                        NSURLResponse *response,
                        NSError *error) {
        // 2
        UIImage  *downloadedImage =
          [UIImage imageWithData:
              [NSData dataWithContentsOfURL:location]];
      //3
      dispatch_async(dispatch_get_main_queue(), ^{
        // do stuff with image
         _imageWithBlock.image = downloadedImage;
      });
}];

// 4
[getImageTask resume];
```

Ah ha!现在这个看起来有点像网络代码！

1. 任务总是被sessions创建。任务一旦被基于block的方法创建。记住你仍然可以使用`NSURLSessionDownloadDelegate`来跟踪下载进度。所以你将获得最好的两个单词！（ *hint for challenge *）

    -URLSession:downloadTask
    :didWriteData:totalBytesWritten
    :totalBytesExpectedToWrite:

2. 这里你使用在 `completion handler`提供的本地变量来获取一个指向图片的指针。
3. 最终你能够，例如，更新 `UIIImageView`的图片来显示新的文件。(hint hint ☺)
4. 你总得自己启动任务！
5. 记住我在前面所说的，一个会话也可以创建将要发送消息给代理方法来通知你完成等的任务。

应该长成这样，使用相同的会话从上面：

```objective-c
// 1
NSURLSessionDownloadTask *getImageTask =
  [session downloadTaskWithURL:[NSURL URLWithString:imageUrl]];

[getImageTask resume];
```

1. 这当然是确定使用更少的代码☺ 然而，假如你只这样做，你将什么都看不到。
你需要让你的代理实现一些`NSURLSessionDownloadDelegate`协议的方法。

首先我们需要获得通知当下载完成时：

```objective-c
-(void)URLSession:(NSURLSession *)session
     downloadTask:(NSURLSessionDownloadTask *)downloadTask
didFinishDownloadingToURL:(NSURL *)location
{
  // use code above from completion handler
}
```

再有你需要提供将要下载的文件存放的位置，然后你就可以使用这个来处理图片。

最后，假如你需要跟踪下载进度，对于任务创建方法，你需要像下面这样用：

```objective-c
-(void)URLSession:(NSURLSession *)session
     downloadTask:(NSURLSessionDownloadTask *)downloadTask
     didWriteData:(int64_t)bytesWritten
totalBytesWritten:(int64_t)totalBytesWritten
totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
{
  NSLog(@"%f / %f", (double)totalBytesWritten,
    (double)totalBytesExpectedToWrite);
}
```

正如你所见，`NSURLSessionTask`是一匹通过网络来干活的真实的驮马。

#### NSURLSessionTask

目前为止你已经知道`NSURLSessionDataTask`和`NSURLSessionDownloadTask`怎样使用了。这两个的任务是来自他们共同的基类`NSURLSessionTask`，你可以在这类看到：

![networking13](http://ww1.sinaimg.cn/mw690/64124373gw1ezzij54674j20go0d53zc.jpg)

`NSURLSessionTask`在你的会话中是任务的基类；他们只能通过一个会话创建并且它们是下面子类中的一个。

#### NSURLSessionDataTask

这个任务发起HTTP GET请求来从服服务器拉取数据。数据被返回以NSData的形式返回。你应该在随后将其把这个数据转换成正确的数据类型比如`XML`,`JSON`,UIImage，plist等等。

```objective-c
NSURLSessionDataTask *jsonData = [session dataTaskWithURL:yourNSURL
      completionHandler:^(NSData  *data,
                          NSURLResponse  *response,
                          NSError  *error) {
        // handle NSData
}];
```

#### NSURLSessionUploadTask

使用这个类当你需要上传一些东西到web服务器时，使用HTTP `POST` 或者 `PUT` 命令。任务带来也允许你监视网络状况当它正在传输的时候。

上传一张图片：

```objective-c
NSData *imageData = UIImageJPEGRepresentation(image, 0.6);

NSURLSessionUploadTask *uploadTask =
  [upLoadSession uploadTaskWithRequest:request
                              fromData:imageData];
```

在这类任务被创建从一个会话中并且图片以NSData的形式上传。这里也可以通使用一个文件或者流的方法来进行上传。

#### NSURLSessionDownloadTask

`NSURLSessionDownloadTask`让通过远程服务下载文件变得超级简单，并且可以暂停和恢复下载只要你想。这个子类有别于其他两个。

* 这个类型的任务直接写入一个临时文件。
* 在下载会话中将调用` URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite: `来更新状态信息。
* 当任务完成时，`URLSession:downloadTask:didFinishDownloadingToURL: `被调用。这就是你该保存文件从临时位置到一个永久位置的时候。
* 当下载失败或者取消时，你可以让数据重新开始下载。

这个特性将极其有用当你在下载一个`Byte Club`定位 全景照片给你的设备的相机胶卷。你看到的一个下载任务例子在上面下载图片片段中。

##### 上述全部

所有的上述任务被创建在一个暂停的状态；在创建一个任务时你需要调用它的继续方法像下面演示的那样：

```objective-c
[uploadTask resume];
```

当你一次不只管理一个任务时，`taskIdentifier`接口允许你唯一标示一个在会话中的任务

这就是！既然你已经知道了`NSURLSession`系列的主要类，让我们尝试一下。

#### Sharing notes with NSURLSession

OK，这不是死亡诗社，这是`Byte Club`!是时候开始看看一下这个的网络代码在起作用了。

你需要一个方法来给`Byte Club`的其他成员发送消息。既然你已经设置了接入token，下一步就是实例化`NSURLSesssion`对象，让后调用你的第一个Dropbox API。

##### Creating an NSURLSession

添加下面的接口到 **NotesViewController.m** 文件，就在 NSArray  *notes 行的后面：

```objective-c
@property (nonatomic, strong) NSURLSession *session;
```

你将创造所有你的下属从上面的session中。

添加下面的方法到`NotesViewController.m`就在`initWithStyle`方法的上面：


```objective-c
- (id)initWithCoder:(NSCoder *)aDecoder
{
    self = [super initWithCoder:aDecoder];
    if (self) {
        // 1
        NSURLSessionConfiguration  *config = [NSURLSessionConfiguration ephemeralSessionConfiguration];

        // 2
        [config setHTTPAdditionalHeaders:@{@"Authorization": [Dropbox apiAuthorizationHeader]}];

        // 3
         _session = [NSURLSession sessionWithConfiguration:config];
    }
    return self;
}
```

下面是上面代码注释的注释的解释：

1. 你的app调用 `initWithCoder`当你实例化一个控制器从一个故事版中;因此这是一个完美的时刻初始化和创建`NSURLSession`。你并不想积极缓存或者持久化这里，所有你使用`ephemeralSessionConfiguration`便利方法，它返回一个没有持久化缓存，cookies，或者认证存储的会话。这是一个"私有浏览"配置。
2. 下一步，你添加授权HTTP头道配置对象中。apiAuthorizationHeader是一个我写的辅助方法，返回一个字符串，以授权制定的格式。这个字符串包含access token，token secret和你的 Dropbox App API 秘钥。记住这是必要的因为每个对 Dropbox API 的调用都需要被授权。
3. 最后，你使用上面的配置创建了`NSURLSession`。

会话现在准备好创建在你app中你所需要的任何网络任务。

##### GET Notes through the Dropbox API

为了模拟一条笔记被另一个用户添加，添加任何你在设置在你的Dropbox根目录下的文件夹中选择的文本文件。例如位于Dropbox文件夹下 **byteclub** 下面显示的 **test.txt**：

![networking14](http://ww3.sinaimg.cn/mw690/64124373gw1ezzkkwyf6ej20go03tglw.jpg)

等待直到`Dropbox`确认它已经同步完你的文件，让后继续下面代码。

添加下面的代码到空的`notesOnDropBox`方法中在`NotesViewController.m`:

```objective-c
[UIApplication sharedApplication].networkActivityIndicatorVisible = YES;
// 1
NSURL *url = [Dropbox appRootURL];

// 2
NSURLSessionDataTask *dataTask =
[self.session dataTaskWithURL:url
            completionHandler:^(NSData  *data,
                                NSURLResponse  *response,
                                NSError  *error) {
    if (!error) {            
        // TODO 1: More coming here!
    }                
}];

// 3  
[dataTask resume];
```

这个方法的目标是检索在app的Dropbox文件下的文件列表。让我们来重温一下这是怎样工作的一步步。

1. 在Dropbox中，你能看到一个文件夹的内容通过进行一个已经授权的`GET`请求到某个特别的URL - 像https://api.dropbox.com/1/metadata/dropbox/byteclub. 我已经创建了一个便利的方法在Dropbox类中来为你产生这个URL。
2. `NSURLSession`用便利构造方法来简单的创建各种类型的任务。这是你创建的一个数据任务为了执行一个GET请求到那个URL。当请求完成时，你的`completionHandler` block被调用。一会儿你将添加一下代码到这里。
3. 记住一个任务默认是一个`暂停`的状态,因此你需要调用恢复方法来启动运行。

那就是所有你需要做的来开始一个`GET`请求-现在让我们添加代码到解析的结果中。添加下面的这些行到"TUDO 1"注释的后面:

```objective-c
// 1
NSHTTPURLResponse *httpResp = (NSHTTPURLResponse *) response;
if (httpResp.statusCode == 200) {

    NSError  *jsonError;

    // 2
    NSDictionary  *notesJSON =
      [NSJSONSerialization JSONObjectWithData:data                                                                        
        options:NSJSONReadingAllowFragments                                                                             
        error:&jsonError];

    NSMutableArray  *notesFound = [[NSMutableArray alloc] init];

    if (!jsonError) {                    
        // TODO 2: More coming here!
    }
}
```

下面是两个主要的部分:

1. 你知道你已经发送一个HTTP请求，所以响应将是一个HTTPP响应。因此这里你可以抛出`NSURLResponse`到一个`NSHTTPURLRequest`响应以便你能够接入到接口的状态码。
假如你收到了一个HTTP状态码200，然后一切正常。

	HTTP 错误码举例：

	* 400 - 输入参数错误。错误消息将表明那个和为什么错误。
	* 401 - token错误或者失效。这个可能发生假如用户或者`Dropbox`被撤销或者接入token过期。你可以通过重新授权修复这个用户。
	* 403 - 错误的授权请求（错误的用户键，坏的随机数，时间戳过期...）。不信的是，重新授权用户在这里并没有用。
	* 404 - 指定路径下的文件或者文件夹没找到。
	* 405 - 未知的请求方法（通常应该是 GET 或者 POST）。
	* 429 - 你的app发送太多请求超出了限制的速率，429能够触发在每个app或者每个用户根部。
	* 503 - 假如响应包括重发后的头，这就意味做你的`OAuth` 1.0 app 正在被限速。否则，这个表明了一个短暂的服务器错误，并且你的app应该重新发送这这个请求。
	* 507 - 用户超出 Dropbox 储存配额。
	* 5xx - 服务器错误。

2. Dropbox API返回JSON类型的数据。所有你收到一个200的响应，然后应该把数据转发成JSON使用iOS的构建JSON序列化的方法。了解更多关于JSON和NSJSONSerialization，查看第23章在 `iOS 5 by Tutorials`,"Working with JSON."

JSON 数据返回从Dropbox将看起来像下面这样：

```
{
    "hash": "6a29b68d106bda4473ffdaf2e94c4b61",
    "revision": 73052,
    "rev": "11d5c00e1cf6c",
    "thumb_exists": false,
    "bytes": 0,
    "modified": "Sat, 10 Aug 2013 21:56:50 +0000",
    "path": "/byteclub",
    "is_dir": true,
    "icon": "folder",
    "root": "dropbox",
    "contents": [{
        "revision": 73054,
        "rev": "11d5e00e1cf6c",
        "thumb_exists": false,
        "bytes": 16,
        "modified": "Sat, 10 Aug 2013 23:21:03 +0000",
        "client_mtime": "Sat, 10 Aug 2013 23:21:02 +0000",
        "path": "/byteclub/test.txt",
        "is_dir": false,
        "icon": "page_white_text",
        "root": "dropbox",
        "mime_type": "text/plain",
        "size": "16 bytes"
    }],
    "size": "0 bytes"
}
```

所有最后一段添加的代码是拉取的部分你感兴趣的从JSON中。特别的，你想循环遍历“contents”数组来把“is_dir”设置成`false`。

这样做，添加下面的代码到“TODO 2”注释后面：

```objective-c
// 1
NSArray *contentsOfRootDirectory = notesJSON[@"contents"];

for (NSDictionary *data in contentsOfRootDirectory) {
    if (![data[@"is_dir"] boolValue]) {
        DBFile  *note = [[DBFile alloc] initWithJSONData:data];
        [notesFound addObject:note];
    }
}

[notesFound sortUsingComparator:
  ^NSComparisonResult(id obj1, id obj2) {
    return [obj1 compare:obj2];                    
}];

self.notes = notesFound;

// 6
dispatch_async(dispatch_get_main_queue(), ^{
    [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
    [self.tableView reloadData];
});
```

这里有两部分：

1. 你拉取数组对象从“contents”键中，让后循环便利数组。每个数组入口是一个文件，所以你创建一个相应的`DBFile`文件模型为每一个文件。

    `DBFile`是一个我为你创建的辅助类，为了拉取信息从一个JSON字典到一个文件中 - 轻轻一瞥就能看到它是怎样工作的。

    当你完成时，你添加所有的笔记到`self.notes`接口中。表视图被设置来显示数组中的任何记录。

既然你的表视图数据源已经更新了，你需要重载表的数据。无论何时你正在处理异步网络请求时，你必须保证更新UIKit在主线程。

机敏的读者将会注意到在上述代码中没有错误处理；假如你感觉你像一个哭丧女（大多数Byte Club 都是！）添加一些代码在这里（在随后的代码块中你将添加），代码在错误和警告用户的时候将重试。

构建运行你的app；你应该看你添加到你的`Dropbox`文件夹的文件是否显示在列表中，就像下面例子一样显示：

![networking16](http://ww2.sinaimg.cn/mw690/64124373gw1ezzmc48hezj20aa0gojs1.jpg)

事情本来是小事，当时这证明了你正确的调用了Dropbox API。

下一步是发布比较然后向其他俱乐部成员发起挑战，再一次使用Dropbox API 就当你是传送机构。

##### POST Notes through the Dropbox API

轻点右上角的 + 号，你将看到笔记add/edit屏幕出现，就像下面演示的一样：

![networking17](http://ww4.sinaimg.cn/mw690/64124373gw1ezzmc5whzij20a90gojru.jpg)

开始的app已经安装了DBFile 模型对象到NoteDetailsViewController在`prepareForSegue:sender:`方法中：

加入你瞥一眼这个方法，你将看到`NoteViewController`被设置成`NoteDetailsViewController`的代理。这种方法，`NoteDetailsViewController`能够通知`NoteViewController`当用户完成编辑一篇笔记或者取消编辑一篇笔记时。

打开`NotesViewController.m `,添加下面这行到`prepareForSegue:sender:`中，就在`showNote.delegate = self`行的后面；

```objective-c
showNote.session = _session;
```

`NoteDetailsViewController`已经有一个`NSURLSession`的接口名字叫做`session`,因此你能够设置它在`prepareForSegue:sender: `载入之前。

现在detail view controller将获得相同的`NSURLSession`,所有detail view controller能够使用它来进行DropBox 的API调用。

`Cancel`和`Done`按钮已经呈现在你的app中；你只需要添加一些在他们背后保存或者取消在尚未完工的笔记逻辑。

在`NoteDetailsViewController.m`,找到下面这一行在`(IBAction)done:(id)sender:`方法中：

```objective-c
// - UPLOAD FILE TO DROPBOX - //
    [self.delegate noteDetailsViewControllerDoneWithDetails:self];
```

...用下面的替换它：

```objective-c
// 1
NSURL  *url = [Dropbox uploadURLForPath: _note.path];

// 2
NSMutableURLRequest *request =
  [[NSMutableURLRequest alloc] initWithURL:url];
[request setHTTPMethod:@"PUT"];

// 3
NSData *noteContents = [_note.contents dataUsingEncoding:NSUTF8StringEncoding];

// 4
NSURLSessionUploadTask *uploadTask = [_session      
  uploadTaskWithRequest:request                                                                  
  fromData:noteContents                                                        
  completionHandler:^(NSData *data,                                                                             
  NSURLResponse *response,                                                                               
  NSError *error)
{   
   NSHTTPURLResponse *httpResp = (NSHTTPURLResponse*) response;

   if (!error && httpResp.statusCode == 200) {

       [self.delegate noteDetailsViewControllerDoneWithDetails:self];
   } else {
       // alert for error saving / updating note
   }
}];

// 5
[uploadTask resume];
```

这个实现了你需要保存和分享你的笔记的所有事情。假如仔细观察每一块的注释，你将发现做了下面的事：

1. 为了上传一个文件到Dropbox，你需要再次使用某个API URL。就像先前你需要一个URL来列出在一个文件夹中的文件，我已经构造了一个辅助方法来为你产生URL。你可以在这里调用。
2. 下一步是你的老朋友`NSMutableURLRequest`,新的APIs能够同时使用普通的URL是和`NSURLRequest`对象，但是这里你需要可变的形式来使Dropbox API让它的请求变成PUT请求。设置HTTP方法作为PUT发信号给Dropbox来让它为你创建一个新的文件。
3. 下一步是将文本从你的`UITextView`编码成NSData对象。
4. 既然你已经创建好了请求和NSData数据，你下一步就是创建一个`NSURLSessionUploadTask`然后设置`completion handler` block.一旦成功，你就调用代理方法` noteDetailsViewControllerDoneWithDetails:`来关闭呈现的内容。在生产级别的应用中你可以回传一个新的BDFile给代理然后同步你需要持久化的数据。为了这个应用，你只需要刷新`NotesViewController `用一个网络调用。
5. 再次提到，所有的任务以暂停的状态被创建，所以你必须调用恢复来启动他们。

构建然后运行你的App，点击笔记标签上的+号。在`challenge name`字段上输入你的名字，输入一些文本在`note`字段想Ray发布一份挑战书，和下面的例子相似：

![networking17](http://ww3.sinaimg.cn/mw690/64124373gw1f008ujjji0j20aa0godgy.jpg)

当你清点`Done`时，`NoteViewController`将返回并且给你列出新的笔记像下面显示的那样：

![networking18](http://ww3.sinaimg.cn/mw690/64124373gw1f008ukjg7bj20aa0go3z4.jpg)
)

你已经正式的给Ray下发挑战书；然而，他有朋友在非常高的位置所有你最好尽力完成这场比赛。

但是这里有一条非常重要的消息遗漏了。你能告诉我是什么么？

轻点笔记包含的挑战；`NoteDetailsViewController`自己呈现，当时笔记的内容确实空白的。

Ray并不会找到你的发的非常有威胁的挑战假如他没有读的话！

现在，app只是调用`Dropbox`元数据API来检索文件列表。你也需要添加一些代码来抓取笔记的内容。

打开`NoteDetailsViewController.m`,用下面的实现替换空白的`retreiveNoteText`的实现：

```objective-c
-(void)retreiveNoteText
{
    // 1
    NSString  *fileApi =
      @"https://api-content.dropbox.com/1/files/dropbox";
    NSString  *escapedPath = [ _note.path
      stringByAddingPercentEscapesUsingEncoding:
      NSUTF8StringEncoding];

    NSString  *urlStr = [NSString stringWithFormat: @"%@/%@",
      fileApi,escapedPath];

    NSURL  *url = [NSURL URLWithString: urlStr];

    [UIApplication sharedApplication].networkActivityIndicatorVisible = YES;

    // 2
    [[ _session dataTaskWithURL:url completionHandler:^(NSData  *data, NSURLResponse  *response, NSError  *error) {

        if (!error) {
            NSHTTPURLResponse  *httpResp = (NSHTTPURLResponse*) response;
            if (httpResp.statusCode == 200) {
                // 3
                NSString  *text =
                 [[NSString alloc]initWithData:data
                   encoding:NSUTF8StringEncoding];
                dispatch_async(dispatch_get_main_queue(), ^{
                    [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
                    self.textView.text = text;
                });

            } else {
                // HANDLE BAD RESPONSE //
            }
        } else {
            // ALWAYS HANDLE ERRORS :-] //
        }
        // 4
    }] resume];
}
```

上面在笔记中的代码（没有错误检查）下面来解释：

1. 设置请求的路径和你期望检索的文件的URL地址；/文件的端点在Dropbox API中将会返回给你一个指定文件的内容。
2. 用指向感兴趣的文件的URL创建数据任务。这个调用应该开始，只要你纵览整个app你会相当熟悉。
3. 假如你响应的代码表明所有的都是好的，在主线程用你在先前的步骤中检索到的文件内容设置textView。记住，UI更新必须切换到主线程。
4. 一旦这个任务被初始化，调用恢复。这里有写不一样的方法和以前的相比，当恢复被直接调用时在任务还没有指派时。

构建运行你的App，在列表中对你的挑战轻点，内容将直接正确的显示在view中，像下面这样：

![networking19](http://ww3.sinaimg.cn/mw690/64124373gw1f008ulhco4j20a90got9d.jpg)

你可以扮演Ray然后通过向笔记中输入文本响应这个挑战；文件将很快更新当你轻点`Done`。

#### 使用`NSURLSessionTask`代理发送照片

你已经看到怎样使用`NSURLSession`异步便利构造方法。但是假如你想把注意力集中在文件传输上，例如上传一个大文件并且显示一个进度条怎么样？

对于这种异步的，耗时任务类型你需要实现`NSURLSessionTaskDelegate`协议方。通过实现这个方法，你能够检索回调当一个任务接收到数据和完成接收数据时。

你可能已经注意到`PanoPhotos`标签是空的当你启动App的时候。然而，`Byte Club`组织的创办成员已经慷慨的提供了一些他们自己的全景照片，你可以用它来填充你的app。

下载这些 我们为你放在一起的[全景照片](http://cdn4.raywenderlich.com/downloads/ByteClub_photos.zip)。解压文件，拷贝到你的app在Dropbox目录下的照片目录。你文件夹的内容应该和下面一样：

![networking20](http://ww3.sinaimg.cn/mw690/64124373gw1f00a5m7rhhj20go06ddhi.jpg)

Dropbox和核心API可以提供照片的缩略图；使用一个 UITableView cell这听起来想一件非常完美的事。

打开` PhotosViewController.m`然后在`“GO GET THUMBNAILS`注释后面添加下面的代码到`tableView:cellForRowAtIndexPath: `

```objective-c
[UIApplication sharedApplication].networkActivityIndicatorVisible = YES;
NSURLSessionDataTask *dataTask = [_session dataTaskWithURL:url
  completionHandler:^(NSData  *data, NSURLResponse  *response,
  NSError  *error) {
    if (!error) {
      UIImage  *image = [[UIImage alloc] initWithData:data];
      photo.thumbNail = image;
      dispatch_async(dispatch_get_main_queue(), ^{
        [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
        cell.thumbnailImage.image = photo.thumbNail;
      });
    } else {
      // HANDLE ERROR //
    }
}];
[dataTask resume];
```

上面的代码显示了照片的缩略图图在表视图的cell中。。。或者至少它会是，假如`_photoThumbnails`现在不是空的话。

找到`refreshPhotos`用下面的实现替换：

```objective-c
- (void)refreshPhotos
{
    [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:YES];
    NSString  *photoDir = [NSString stringWithFormat:@"https://api.dropbox.com/1/search/dropbox/%@/photos?query=.jpg",appFolder];
    NSURL  *url = [NSURL URLWithString:photoDir];

    [[ _session dataTaskWithURL:url completionHandler:^(NSData
       *data, NSURLResponse  *response, NSError  *error) {
        if (!error) {
            NSHTTPURLResponse  *httpResp =
             (NSHTTPURLResponse*) response;
            if (httpResp.statusCode == 200) {

                NSError  *jsonError;
                NSArray  *filesJSON = [NSJSONSerialization  
                  JSONObjectWithData:data                                                                     
                  options:NSJSONReadingAllowFragments                                                                           
                  error:&jsonError];
                NSMutableArray  *dbFiles =
                  [[NSMutableArray alloc] init];

                if (!jsonError) {
                    for (NSDictionary  *fileMetadata in
                      filesJSON) {
                        DBFile  *file = [[DBFile alloc]
                          initWithJSONData:fileMetadata];
                        [dbFiles addObject:file];
                    }

                    [dbFiles sortUsingComparator:^NSComparisonResult(id obj1, id obj2) {
                        return [obj1 compare:obj2];
                    }];

                     _photoThumbnails = dbFiles;

                    dispatch_async(dispatch_get_main_queue(), ^{
                        [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:NO];
                        [self.tableView reloadData];
                    });
                }
            } else {
                // HANDLE BAD RESPONSE //
            }
        } else {
            // ALWAYS HANDLE ERRORS :-] //
        }
    }] resume];
}
```

这个和你早期载入挑战笔记时写的代码很像。这次，API调用来查找在`photos`目录的内容，并且只会以.jpg拓展的文件。

既然`_photoThumbnails`数组已经填充好了，缩略图将出现在表视图中并且异步更新。

构建运行你的app，然后切换到`PanoPhotos`标签；缩略图将载入并且像下面这样出现：

！[networking21](http://ww2.sinaimg.cn/mw690/64124373gw1f00ahhaw20j20a90go0uz.jpg)

照片看起来非常的棒--只是请当心Matthijs家撕裂代码的猫🐱！

#### 上传一张全景照片

你的app能够下载相片，如果它也能上传照片并且显示上传进度的话就非常棒了。

为了跟踪上传的进度，`PhotosViewController`必须成为`NSURLSessionDelegate`和`NSURLSessionTaskDelegate`协议的代理，以便你能收到进度回调。

修改在`PhotosViewController.m`中`PhotosViewController`的接口声明，添加`NSURLSessionTaskDelegate`,像下面这样：

```objective-c
 @ interface PhotosViewController ()UITableViewDelegate, UITableViewDataSource, UIImagePickerControllerDelegate, UINavigationControllerDelegate, NSURLSessionTaskDelegate>
```

下一步，添加下面的私有接口：

```objective-c
@property (nonatomic, strong)
  NSURLSessionUploadTask *uploadTask;
```

上面的指针引用了任务对象；通过哪种方式，你就可以接入到对象的成员中来跟踪上传任务的进度了。

当用户选择一张图片上传时，`didFinishPickingMediaWithInfo`调用`uploadImage:`方法来执行文件上传。
现在，那个方法空了-这是你的工作让它丰满起来。

替换`uploadImage: `用下面的代码：

```objective-c
- (void)uploadImage:(UIImage*)image
{
    NSData  *imageData = UIImageJPEGRepresentation(image, 0.6);

    // 1
    NSURLSessionConfiguration  *config = [NSURLSessionConfiguration defaultSessionConfiguration];
    config.HTTPMaximumConnectionsPerHost = 1;
    [config setHTTPAdditionalHeaders:@{@"Authorization": [Dropbox apiAuthorizationHeader]}];

    // 2
    NSURLSession  *upLoadSession = [NSURLSession sessionWithConfiguration:config delegate:self delegateQueue:nil];

    // for now just create a random file name, dropbox will handle it if we overwrite a file and create a new name..
    NSURL  *url = [Dropbox createPhotoUploadURL];

    NSMutableURLRequest  *request = [[NSMutableURLRequest alloc] initWithURL:url];
    [request setHTTPMethod:@"PUT"];

    // 3
    self.uploadTask = [upLoadSession uploadTaskWithRequest:request fromData:imageData];

    // 4
    self.uploadView.hidden = NO;
    [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:YES];

    // 5
    [ _uploadTask resume];
}
```

下面是上面的代码做的事：

1. 起先，你使用设置在`initWithCoder`的会话和相关的便利方法来创建异步任务。这个时候，你使用一个`NSURLSessionConfiguration `,它只允许一个连接连接到远程主机，因为你上传进度处理一次就是一个文件。
2. 上传和下载任务报告信息通过它们的代理返回；你将简短的实现。
3. 这里你设置了`uploadTask`接口使用从`UIImagePicker`获得的JPEG图片。
4. 下一步，你显示`UIProgressView`让它隐藏在`PhotosViewController`内部。
5. 开始任务--额，抱歉，是恢复任务。

既然代理已经设置了，你就可以实现`NSURLSessionTaskDelegate`方法来更新进度视图。

添加下面的代码到` PhotosViewController.m`文件末尾：

```objective-c
#pragma mark - NSURLSessionTaskDelegate methods

- (void)URLSession:(NSURLSession *)session
  task:(NSURLSessionTask *)task
  didSendBodyData:(int64_t)bytesSent
  totalBytesSent:(int64_t)totalBytesSent
  totalBytesExpectedToSend:(int64_t)totalBytesExpectedToSend
{
    dispatch_async(dispatch_get_main_queue(), ^{
        [ _progress setProgress:
          (double)totalBytesSent /
          (double)totalBytesExpectedToSend animated:YES];
    });
}
```

上面的代理方法将定期报告信息给调用者关于上传任务的信息。它同时会更新`UIProgressView`（ _progress）的进度以便显示 totalBytesSent/totalBytesExpectedToSend,这比显示一个完成的百分比跟有意义（也更极客）。

剩下唯一的事就是当上传任务结束时指示一下。添加下面的代码到` PhotosViewController.m`文件末尾：

```objective-c
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
    // 1
    dispatch_async(dispatch_get_main_queue(), ^{
        [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:NO];
         _uploadView.hidden = YES;
        [ _progress setProgress:0.5];
    });

    if (!error) {
        // 2
        dispatch_async(dispatch_get_main_queue(), ^{
            [self refreshPhotos];
        });
    } else {
        // Alert for error
    }
}
```

这里没有很多代码，但是它执行了两项非常重要的任务：

1. 打开网络指示器，然后隐藏`_uploadView`作为上传完成的一点点清理工作。
2. 刷新`PhotosViewController`以便包含你刚刚上传的照片，由于demo app是不能进行任何本地储存的上传
。在一个真正的app中，你应该把图片在本地进行储存和缓存。

构建运行你的app，导航到`PanoPhotos`标签，点击照相图标选择一张照片。

![networking22](http://ww4.sinaimg.cn/mw690/64124373gw1f00bn13wq4j20di09ojs2.jpg)

> 注意：假如你说使用模拟器测试app，很显然你不能用你的Mac拍照，所有仅仅是拷贝一张全景照片
给模拟器然后上传。这样做，可以确保没有其他的Xcode工程现在连接到这个模拟器，在Xcode中选择 **Xcode   Open Developer Tool   iOS Simulator**。

> 从Finder中拖拽一张全景照片带模拟器中，在模拟器中图片将会在Safari中打开。长按图片然后保存图片到图库中。

在选择一张图片后上传，uploadView显示在屏幕的中央，并且带有上传进度，乡下面这一样显示：

！[networking23](http://ww1.sinaimg.cn/mw690/64124373gw1f00bn4hmqwj209e0gomzu.jpg)

你可能注意到一张图片上传需要花一些时间由于上传任务设置了`better quality`缩放因子。对于那些A类性格的人，你应该提供一个取消函数假如上传花费太长的时间。

取消按钮在`uploadView`已经被封装起来在故事板中，所有你只需实现清楚逻辑来杀死下载操作就行。

用下面的代码替换` PhotosViewController.m`的`cancelUpload: `：

```objective-c
- (IBAction)cancelUpload:(id)sender {    
    if ( _uploadTask.state == NSURLSessionTaskStateRunning) {
        [ _uploadTask cancel];
    }
}
```

在这类你会看到，取消一个任务相当简单就是调用一个取消方法。

现在构建运行你的app，选择一张照片上传然后点击`Cancel`。图片上传将会停止并且`uploadView`将会被隐藏。

就这样--`Byte Club`完成了！

#### 何去何从？

这里是[完成的工程](http://cdn5.raywenderlich.com/downloads/ByteClub_Completed.zip)在这个`NSURLSession`教程中。

假如你做到这一步，恭喜你可以享受到`Byte Club`的时光！不要告诉任何`Android`的小伙伴们！ :]
你现在能够处理在你app需要的任何网络任务了。

假如你喜欢这个课程，你可能想要查阅我们的新书[ iOS 7 by Tutorials](http://www.raywenderlich.com/?page_id=48020),这是这本书的一个简略版本，这本书几乎涵盖了在iOS 7中最新和最多的APIs，这些你应该清楚的知道作为一个开发者。

我几乎忘记了。。。

    /slap <you the reader> have been slapped around a bit with a large trout.

（不要问我为什么 hehe！😄）

假如你有任何问题或者评论关于这个教程或者`NSURLSession`,请加入到下面的论坛讨论！


参考资料：

* [从 NSURLConnection 到 NSURLSession](http://objccn.io/issue-5-4/)
* [NSURLSession Tutorial](http://www.raywenderlich.com/51127/nsurlsession-tutorial)


译者注：欢迎转载，但请一定注明出处！ <http://blog.wangruofeng007.com>
