---
layout: post
title: iOS 多线程
date: 2016-06-11
excerpt: "多线程详解"
tags: 
- iOS基础
comments: true
feature: http://LSnail.github.io/assets/pictures/13044471,1440,900.jpg

---

# 概述

多线程是实现单个应用程序内多个代码的执行路径。概念不再赘述，直接开始正题。

PS：Swift中并没有使用新一套的线程，而是继续使用OC原有的一套线程。

在iOS中目前有**4**套多线程实现的方案：

	* Pthreads
	* NSThread
	* Cocoa NSOperation (NSOperation & NSOperationQueue)
	* GCD (Grand Central Dispatch)
	
这里稍微介绍一下它们的特点。

#### 1. Pthreads

是的，比较生疏的一种，因为我不会使用它，或者说因为它是基于`C语言`的框架，可以在很多操作系统上通用的多线程API，平常在开发中并不会用到，所以想要从底层开始定制一套多线程方案的话，它也许是个不错的选择。具体的可以自行搜索用法。

#### 2. NSThread

这套方案是经过Apple封装过的，完全面向对象的。
轻量级上来说是最轻的，但需要自己管理线程的生命周期和线程同步。线程同步对数据的加锁会有一定的系统开销。

#### 3. NSOperation

NSOperation不需要关心线程管理和数据同步的事情，可以把精力放在需要执行的操作上。
相关的类有`NSOperation`和`NSOperationQueue`，具体用法见下文。

#### 4. GCD

重点来了，GCD是Apple开发的一个多核编程的解决方案，可以自动合理的利用现有资源（多个CPU内核），同时自动管理线程的生命周期。
GCD是一个替代以上三种方案的高效强大的技术，也是Apple最推荐使用的套路 :)


# NSThread 详解

首先来在ViewController中创建一个线程：

~~~ruby
// "类方法--直接创建并且自动启动"
let thread = NSThread.detachNewThreadSelector(#selector(ViewController.downLoadTask), toTarget: self, withObject: nil)

// "实例方法--创建一个线程对象，然后手动运行线程"
let thread = NSThread(target: self, selector: #selector(ViewController.runThread), object: nil)

thread.start()

// "PS:由于我的代码高亮样式没有调整好，所以#selector这部分比较灰暗，见谅 ^_^"
~~~


