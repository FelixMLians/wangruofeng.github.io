---
layout: post
title: "iOS线程安全-锁"
date: 2016-01-14 02:08:36 +0800
comments: true
tags: [iOS, Networking, NSURLSession, Octopress]
keywords: Octopress, iOS, Multithreading, Lock, Swift, Objective-c,
categories: iOS Multithreading
description: Multithreading
---

### “Object－C”语言

1.**使用 @synchronized 类实现锁**

```objective-c

// 实例类person
Person *person = [[Person alloc] init];
// 线程A
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    @synchronized(person) {
        [person personA];
        [NSThread sleepForTimeInterval:3];
        // 线程休眠3秒
    }
});
// 线程B
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    @synchronized(person) {
        [person personB];
    }
});

```

2.**使用 NSRecursiveLock 类实现锁**
> 递归锁，递归或循环方法时使用此方法实现锁，可避免死锁等问题


```objective-c

// 实例类person
Person *person = [[Person alloc] init];

// 创建锁对象
NSRecursiveLock *theLock = [[NSRecursiveLock alloc] init];

// 创建递归方法
static void (^testCode)(int);
testCode = ^(int value) {
    [theLock tryLock];
    if (value > 0)  {
        [person personA];
        [NSThread sleepForTimeInterval:1];
        testCode(value - 1);
    }
    [theLock unlock];
};

//线程A
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    testCode(5);
});

//线程B
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    [theLock lock];
    [person personB];
    [theLock unlock];
});

```

3.**使用 NSConditionLock（条件锁）类实现锁**
> 使用此方法可以指定，只有满足条件的时候才可以解锁

```objective-c

// 实例类person
Person *person = [[Person alloc] init];
// 创建条件锁
NSConditionLock *conditionLock = [[NSConditionLock alloc] init];
// 线程A
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    [conditionLock lock];
    [person personA];
    [NSThread sleepForTimeInterval:5];
    [conditionLock unlockWithCondition:10];
 });
// 线程B
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    [conditionLock lockWhenCondition:10];
    [person personB];
    [conditionLock unlock];
 });

```

4.**NSDistributedLock（分布式锁）**
> 在iOS中不需要用到，也没有这个方法，因此本文不作介绍，这里写出来只是想让大家知道有这个锁存在。
如果想要学习NSDistributedLock的话，你可以创建MAC OS的项目自己演练，方法请自行Google，谢谢

### C语言
1.**使用 pthread_mutex_t 实现锁**
> 注意：必须在头文件导入：#import <pthread.h>

```objective-c

// 实例类person
Person *person = [[Person alloc] init];
// 创建锁对象
__block pthread_mutex_t mutex;
pthread_mutex_init(&mutex, NULL);
// 线程A
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    pthread_mutex_lock(&mutex);
    [person personA];
    [NSThread sleepForTimeInterval:5];
    pthread_mutex_unlock(&mutex);
});
// 线程B
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    pthread_mutex_lock(&mutex);
    [person personB];
    pthread_mutex_unlock(&mutex);
});

```
2.**使用 GCD 实现“锁”(信号量）**
> GCD提供一种信号的机制，使用它我们可以创建“锁”

```objective-c
// 实例类person
Person *person = [[Person alloc] init];

// 创建并设置信量
dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);

// 线程A
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    [person personA];
    [NSThread sleepForTimeInterval:5];
    dispatch_semaphore_signal(semaphore);
});

// 线程B
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    [person personB];
    dispatch_semaphore_signal(semaphore);
});

```

我在这里解释一下代码。`dispatch_semaphore_wait`方法是把信号量加1，`dispatch_semaphore_signal`
是把信号量减1。

我们把信号量当作是一个计数器，当计数器是一个非负整数时，所有通过它的线程都应该把这个整数减1。

如果计数器大于0，那么则允许访问，并把计数器减1。如果为0，则访问被禁止，所有通过它的线程都处于
等待的状态。

3.**使用POSIX（条件锁）创建锁）**


```objective-c
// 实例类person
Person *person = [[Person alloc] init];

// 创建互斥锁
 __block pthread_mutex_t mutex;
 pthread_mutex_init(&mutex, NULL);

 // 创建条件锁
 __block pthread_cond_t cond;
 pthread_cond_init(&cond, NULL);

 // 线程A
 dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
     pthread_mutex_lock(&mutex);
     pthread_cond_wait(&cond, &mutex);
     [person personA];
     pthread_mutex_unlock(&mutex);
 });

 // 线程B
 dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
     pthread_mutex_lock(&mutex);
     [person personB];
     [NSThread sleepForTimeInterval:5];
     pthread_cond_signal(&cond);
     pthread_mutex_unlock(&mutex);
 });

```

效果：程序会首先调用线程B，在5秒后再调用线程A。因为在线程A中创建了等待条件锁，线程B有激活锁，只有当线程B执行完后会激活线程A。

`pthread_cond_wait`方法为等待条件锁。

`pthread_cond_signal`方法为激活一个相同条件的条件锁。


参考资料：<http://www.liuhaihua.cn/archives/25316.html>

备注：欢迎转载，但请一定注明出处！ <http://blog.wangruofeng007.com>
