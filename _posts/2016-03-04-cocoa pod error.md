---
layout: post
title: CocoaPods报错：The dependency is not used in any concrete target
date: 2016-03-04
excerpt: "podfile升级之后到最新版本，pod里的内容必须明确指出所用第三方库的target，否则会出现 `The dependency is not used in any concrete target` 这样的错误。"
tags: 
- iOS基础
comments: true
feature: http://i.imgur.com/Ds6S7lJ.png

---




podfile升级之后到最新版本，pod里的内容必须明确指出所用第三方库的target，否则会出现The dependency `` is not used in any concrete target这样的错误。

以下从三个步骤陈述：

### 1. Podfile升级

查看pod版本：


pod --version



pod 升级（此时是升级到1.0.0.beta.2版本）：


sudo gem install cocoapods --pre




### 2. 错误内容：

我的Podfile的内容是：

~~~ruby
platform :ios, ‘7.0‘

pod ‘ReactiveCocoa‘, ‘2.1.8‘
pod ‘objectiveflickr‘, ‘2.0.4‘
pod ‘LinqToObjectiveC‘, ‘2.0.0‘
pod ‘SDWebImage‘, ‘3.6‘
~~~



pod install后出现错误The dependency `` is not used in any concrete target

~~~ruby
pod install
/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/2.0.0/universal-darwin14/rbconfig.rb:213: warning: Insecure world writable dir /Applications/Cocos/tools/ant/bin in PATH, mode 040777
Re-creating CocoaPods due to major version update.
Updating local specs repositories
Analyzing dependencies
[!] The dependency `ReactiveCocoa (= 2.1.8)` is not used in any concrete target.
The dependency `objectiveflickr (= 2.0.4)` is not used in any concrete target.
The dependency `LinqToObjectiveC (= 2.0.0)` is not used in any concrete target.
The dependency `SDWebImage (= 3.6)` is not used in any concrete target.
~~~



### 3. 修改方法：

Podfile内容更改：

~~~ruby
platform :ios, ‘7.0‘

target "RWTFlickrSearch" do
pod ‘ReactiveCocoa‘, ‘2.1.8‘
pod ‘objectiveflickr‘, ‘2.0.4‘
pod ‘LinqToObjectiveC‘, ‘2.0.0‘
pod ‘SDWebImage‘, ‘3.6‘
end
~~~



这样再运行pod install，就会成功了。    
    