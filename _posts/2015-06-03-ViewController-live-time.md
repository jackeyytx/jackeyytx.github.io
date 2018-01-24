---
date: 2015-06-03 17:16:02+00:00
layout: post
title: UIViewController的生命周期及iOS程序执行顺序
excerpt: "简单基础却不能错过的知识点哦~"
feature: http://i.imgur.com/Ds6S7lJ.png
tags: 
- iOS基础
comments: true

---




### 当一个视图控制器被创建，并在屏幕上显示的时候。 代码的执行顺序：


1. `alloc` 创建对象，分配空间
2. `init` (initWithNibName) 初始化对象，初始化数据
3. `loadView` 从nib载入视图 ，通常这一步不需要去干涉。除非你没有使用xib文件创建视图
4. `viewDidLoad` 载入完成，可以进行自定义数据以及动态创建其他控件
5. `viewWillAppear` 视图将出现在屏幕之前，马上这个视图就会被展现在屏幕上了
6. `viewDidAppear` 视图已在屏幕上渲染完成
当一个视图被移除屏幕并且销毁的时候的执行顺序，这个顺序差不多和上面的相反
1. `viewWillDisappear` 视图将被从屏幕上移除之前执行
2. `viewDidDisappear` 视图已经被从屏幕上移除，用户看不到这个视图了
3. `dealloc` 视图被销毁，此处需要对你在init和viewDidLoad中创建的对象进行释放


### 关于`viewDidUnload`

在发生内存警告的时候如果本视图不是当前屏幕上正在显示的视图的话， viewDidUnload将会被执行，本视图的所有子视图将被销毁，以释放内存,此时开发者需要手动对viewLoad、viewDidLoad中创建的对象释放内存。 因为当这个视图再次显示在屏幕上的时候，viewLoad、viewDidLoad 再次被调用，以便再次构造视图。

当我们创建一个UIViewController类的对象时，通常系统会生成几个默认的方法，这些方法大多与视图的调用有关，但是在视图调用时，这些方法的调用顺序如何，需要整理下。

通常上述方法包括如下几种，这些方法都是UIViewController类的方法:

{% highlight ruby %}
- (void)viewDidLoad;
- (void)viewDidUnload;
- (void)viewWillAppear:(BOOL)animated;
- (void)viewDidAppear:(BOOL)animated;
- (void)viewWillDisappear:(BOOL)animated;
- (void)viewDidDisappear:(BOOL)animated;
{% endhighlight %}

### 下面介绍下APP在运行时的调用顺序。

*  `- (void)viewDidLoad；`
  
一个APP在载入时会先通过调用loadView方法或者载入IB中创建的初始界面的方法，将视图载入到内存中。然后会调用viewDidLoad方法来进行进一步的设置。通常，我们对于各种初始数据的载入，初始设定等很多内容，都会在这个方法中实现，所以这个方法是一个很常用，很重要的方法。

但是要注意，这个方法只会在APP刚开始加载的时候调用一次，以后都不会再调用它了，所以只能用来做初始设置。
      
*  `- (void)viewDidUnload;`
     
在内存足够的情况下，软件的视图通常会一直保存在内存中，但是如果内存不够，一些没有正在显示的viewcontroller就会收到内存不够的警告，然后就会释放自己拥有的视图，以达到释放内存的目的。但是系统只会释放内存，并不会释放对象的所有权，所以通常我们需要在这里将不需要在内存中保留的对象释放所有权，也就是将其指针置为nil。
      
这个方法通常并不会在视图变换的时候被调用，而只会在系统退出或者收到内存警告的时候才会被调用。但是由于我们需要保证在收到内存警告的时候能够对其作出反应，所以这个方法通常我们都需要去实现。
      
另外，即使在设备上按了Home键之后，系统也不一定会调用这个方法，因为IOS4之后，系统允许将APP在后台挂起，并将其继续滞留在内存中，因此，viewcontroller并不会调用这个方法来清除内存。

*  `- (void)viewWillAppear:(BOOL)animated;`

系统在载入所有数据后，将会在屏幕上显示视图，这时会先调用这个方法。通常我们会利用这个方法，对即将显示的视图做进一步的设置。例如，我们可以利用这个方法来设置设备不同方向时该如何显示。

另外一方面，当APP有多个视图时，在视图间切换时，并不会再次载入viewDidLoad方法，所以如果在调入视图时，需要对数据做更新，就只能在这个方法内实现了。所以这个方法也非常常用。
*  ` - (void)viewDidAppear:(BOOL)animated；`

有时候，由于一些特殊的原因，我们不能在viewWillApper方法里，对视图进行更新。那么可以重写这个方法，在这里对正在显示的视图进行进一步的设置。
*  ` - (void)viewWillDisappear:(BOOL)animated；`

在视图变换时，当前视图在即将被移除、或者被覆盖时，会调用这个方法进行一些善后的处理和设置。

由于在IOS4之后，系统允许将APP在后台挂起，所以在按了Home键之后，系统并不会调用这个方法，因为就这个APP本身而言，APP显示的view，仍是挂起时候的view，所以并不会调用这个方法。
      
*  `- (void)viewDidDisappear:(BOOL)animated；`

我们可以重写这个方法，对已经消失，或者被覆盖，或者已经隐藏了的视图做一些其他操作。
```

上述方法的流程图可以简单用如下表示：

运行APP —> 载入视图 —> 调用viewDidLoad方法 —> 调用viewWillAppear方法 —> 调用viewDidAppear方法 —>   正常运行 -> 载入新的View
{: .notice}
             
APP需要调用另一个view -> 调用viewWillDisappear -> 调用viewDidDisappear -> 收到内存警告 -> 调用viewDidUnload -> 释放对象所有权
{: .notice}
    
[链接：IOS程序启动执行顺序](http://www.yifeiyang.net/iphone-developer-advanced-3-iphone-application-startup-process/)



### IOS 开发 `loadView` 和 `viewDidLoad` 的区别
iPhone开发必不可少的要用到这两个方法。 他们都可以用来在视图载入的时候，初始化一些内容。 但是他们有什么区别呢？

viewDidLoad 此方法只有当view从nib文件初始化的时候才被调用。
loadView 此方法在控制器的view为nil的时候被调用。 此方法用于以编程的方式创建view的时候用到。 如：

~~~ ruby
- ( void ) loadView {
    UIView *view = [[UIView alloc] initWithFrame:[UIScreen mainScreen].applicationFrame];
    [ view setBackgroundColor:_color];
    self.view = view;
    [view release];
}
~~~

你在控制器中实现了loadView方法，那么你可能会在应用运行的某个时候被内存管理控制调用。 如果设备内存不足的时候， view 控制器会收到didReceiveMemoryWarning的消息。 默认的实现是检查当前控制器的view是否在使用。如果它的view不在当前正在使用的view hierarchy里面，且你的控制器实现了loadView方法，那么这个view将被release, loadView方法将被再次调用来创建一个新的view。


---------------------------------------------------------------


PS:

不要在`-lodadView`里面读取self.view。而是只设置它，不要去拿到它的对象。因为self.view的对象在当前如果没有加载完毕，就会调用-loadView方法，如果在里面读取的话会出现死循环。
按照苹果官方<u>pre-Interface-Builder</u>里面的示例代码，通常我们在`-loadView`里面的做法应该是这样的：

{% highlight ruby %}
UIView *view = [[UIView alloc] init...];
...
[view addSubview: whatever1];
[view addSubview: whatever2];
...
self.view = view;
[view release];
{% endhighlight %}
