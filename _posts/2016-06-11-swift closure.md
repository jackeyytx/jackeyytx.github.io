---
layout: post
title: Swift Closure -- 闭包从入门到进阶
date: 2016-06-11
excerpt: "这里有swift闭包的用法和探寻"
tags: 
- Swift
comments: true
feature: http://LSnail.github.io/assets/pictures/13044471,1440,900.jpg

---

## 写在前面


在我们开始话题之前，先来说说我们可以继续愉快的聊下去的几个前提：

* 你知道swift是一门牛逼的语言(说PHP是最好的语言的童鞋们，你们先放下手里的板砖，有话好好说!)
* 你了解swift的基本语法，包括什么是变量和func，以及怎么写一个func并调用它(“闭嘴，这用你来哔哔哔？”)
* 你现在身怀两门绝技，一种专门对付像我一样的菜鸟，抬手就让我等跪地唱征服，它叫做<a href="https://www.baidu.com" class="btn">百度一下你就知道了</a>；另一种是用来高手过招，一出手风云变幻翻江倒海，下证命理上破虚空，名为<a href="https://www.google.com" class="btn">谷歌一下你就什么都不知道了</a>
* 最重要的一点，你现在一点都不想揍我(你保证么？)，那么，让我们开始听我来详(xia)细(bi)讲(bi)一下，**我所理解的swift闭包**(文章为@Toki_Han原创，引用内容在中间和最后均有注明)


# 什么是闭包


> **闭包（Closures）是独立的函数代码块，能在代码中传递及使用。Swift中的闭包与C和Objective-C中的代码块及其它编程语言中的匿名函数相似。**

> **闭包可以在上下文的范围内捕获、存储任何被定义的常量和变量引用。因这些常量和变量的封闭性，而命名为“闭包（Closures）”。Swift能够对所有你所能捕获到的引用进行内存管理。**

**PS:** 说的这么高大上，所以最后闭包到底是个什么鬼？好吧，其实它就是类似OC里面的`block`(请注意，是类似！并不是同一种东西)，或者类似JavaScript里面的`匿名自执行函数`(JS:"你哪来的，别和我扯进乎哈")。

**再PS:** 那`捕获（capturing）`又是什么鬼？这个东西说出来你可能不信，它其实就是`引用/得到`(get/set)的高大上说法，用于捕捉某个范围内的常量和变量的值。这个有什么意义呢，我们后面会慢慢说。

**再再PS:** 

	* 全局函数都是闭包，特点是有函数名但没有捕获任何值。
	* 嵌套函数都是闭包，特点是有函数名，并且可以在它封闭的函数中捕获值。
	* 闭包表达式都是闭包，特点是没有函数名，可以使用轻量的语法在它所围绕的上下文中捕获值。
	* 变量和常量也可以用闭包来表示。


# 闭包能干嘛


我和你说，闭包老厉害了~大神们写的出神入化的代码，里面肯定少不了各种闭包，先看看人家怎么形容的(来自letsswift.com)：

> **Swift的闭包表达式有着干净，清晰的风格，并常见情况下对于鼓励简短、整洁的语法做出优化。这些优化包括：
	推理参数及返回值类型源自上下文
	隐式返回源于单一表达式闭包
	简约参数名
	尾随闭包语法**

好吧，我墨水少，我不会形容，但是我看明白了，闭包俩作用：
  
**`好看!`**		**`厉害!`**

你想想哈，我用二十行的代码写的一个func，你用三行写出来了，还更简洁明了，调用的时候就像雪碧一样心飞扬，这是一件多有范儿的事情！废话到此为止，let's get it!


# 闭包怎么用（纯干货）


严重申明：我已经看了太多的 `intArray.sortInPlace({$0 >= $1})` 这种代码的帖子了，只想说一句，大神们你们写的很详细了，我也看懂了，但是为啥非要用 `public mutating func sortInPlace()` 这个方法上手很麻烦呐，内心一直有一种不安全感提醒我，真的完全明白了吗？所以，我打算从最基础的 1 + 2 写起，先入门再说。
{: .notice}

是不是已经感觉迫(废)不(话)及(好)待(多)了呢？来写一个闭包来展示下老夫的手段？那先来看看闭包的定义形式：

#### 闭包最全的定义形式是:
~~~ruby
{
    (参数:参数类型) -> 返回值类型  in
    code(return xxxxx)
}(需要传的参数)
~~~

PS：我的理解是，这里的闭包和JavaScript里的匿名自执行函数非常的相似，我们关注的地方在于可以在内部实现什么，而不是在外面如何使用它。

#### 感谢swift提供的类型推导，它可以根据后面括号里传的参数，自动判断参数的类型，所以我们可以把闭包内部的参数的类型省略掉：
~~~ruby
{
    (参数) -> 返回值类型  in
    code(return xxxxx)
}(需要传的参数)
~~~

#### 或者把闭包中的返回值类型省略掉，但是有一点注意，当你把参数和返回值类型同时省略的时候，一定要在外部声明这个闭包返回值类型(要不你什么都不说清楚，代码怎么知道你想要个啥啊)

~~~ruby
{
    (参数) in
    code(return xxxxx)
}(需要传的参数)
~~~

#### 最后，其实参数也是可以省略的，用**`$`**来调用函数内部给参数默认分配的名字。如果闭包中只有简单的一行代码，关键字**`return`**也是可以省略的，这部分请见下面的例子。

#### 正式上代码(以下代码在playground中完成并测试)
这里我就用一个简单的加法运算来说明下，有一点要牢记：**swift中，函数是一等公民！**(有关这个问题请自行施放百度/Google绝技):

~~~ruby
// "不使用闭包来实现两数字相加"
func getSum(a a: Int, b: Int) -> Int {
    return a + b
}

var sum = getSum(a: 3, b: 5)
// "sum = 8"
~~~

~~~ruby
// "现在我们用闭包来实现相同功能"
// "这里的sumClosure实质上是一个(Int, Int) -> Int的func"
let sumClosure = {
    (a: Int, b: Int) -> Int in
    return a + b
}
var sum: Int = sumClosure(3, 5)
// "sum = 8"


// "或者我们干脆这么写(这里就已经是用闭包对变量/属性进行操作了)"
var sum: Int = {
    (a: Int, b: Int) -> Int in
    return a + b
}(3,5)
~~~

现在让我们开始对这个可怜的sum进行各种砍掉砍掉：

~~~ruby
// 1."省略参数类型"
var sum = {
    a, b -> Int in
    return a + b
}(3,5)

// 2."然后省略返回值类型(要注意在外部还要声明类型的哦~)"
var sum: Int = {
    a, b in
    return a + b
}(3,5)

// 3."效果不行，这么长怎么玩，我们把中间那行丑爆了的删掉"
var sum: Int = {
    return $0 + $1 // "关于$是什么意思请自行查找"
}(3,5)

// 4."还可以砍么？答案是当然的！把碍眼的return砍掉！"
var sum: Int = {
    $0 + $1
}(3,5)

// 5."激动人心的时刻到了，把它写在一行里面！"
var sum: Int = {$0 + $1}(3,5)
~~~

如何，最终的效果是不是很高大上？别急，这里有几个**`重要问题`**需要说明：

1. 这里声明了 `var sum: Int` ，主要是为了方便理解，平常用的时候是不会多这么一步的，把闭包当参数/返回值的时候再冒出来一个var，啧啧，水(逼)平(格)一下子就low了很多，所以可以这么写: someFunc(aString,{$0 + $1}(3,5))

2. 这点很重要，那就是为啥别人的闭包后面都没有(3,5)这种影响市容的玩意儿？那是因为啊，我没写一个合适的方法来使用这个闭包。(3,5)的作用，就相当于我立马在声明了这个闭包以后，就马上调用了这个闭包并给它传入参数。现在还是为了方便理解阶段呢，别急嘛~下面来个完整点的

~~~ruby
func printSum(string1 str1: String, string2 str2: String, result: (Int, Int) -> Int) {
    let count = result(str1.characters.count, str2.characters.count)
    print("\(str1) and \(str2)--> total length is: \(count)")
}

printSum(string1: "hello", string2: "world", result: {$0 + $1})
// "打印出的结果为  hello and world--> total length is: 10"
~~~

这里需要说明的是： 当你需要将一个闭包当做参数使用的时候，如果要给闭包本身传参，那就需要有一个合适的、对应的函数参数来接收闭包作为参数。在上面的例子中，printSum这个函数的作用就是打印两个字符串的总长度，这里第三个参数result就被声明为了：接收两个Int参数的闭包函数，并且返回两个参数的和。这也就是有了好车子就得买贵点的保险的道理。
{: .notice}

说到这里，相信童鞋们应该对闭包这货有点大致的理解了吧。其实上面所有的东西加起来，只是说明白(但愿吧)了闭包是什么和怎么写的问题。语法上来说并不难理解，而且闭包的使用也是非常灵活的。用好了，代码美观不说，最大的好处是当别人看的时候...啧啧，你们可以脑补一下 ^o^。多加练习总能写出自己的风(bi)格(ge)来~


Wait~！你以为这就完了？呵呵，太天真了。。。说了是纯干货的，会有这么简单么？来，倒杯咖啡，听我接着给你吹...啊不，接着说一下闭包的进阶形态。


## 属性的闭包

应该知道的是，类的属性可以用闭包来表示，可以在闭包中定义set,get,willSet,willGet等方法。就算是你当时并不清楚这个是闭包，但是肯定见过这样的写法。而且不仅仅是类的属性，变量也是可以这么做的，写法上面并没有区别。下面我们来举个例子：

~~~ruby
// "变量或者属性的闭包表示"
// "get和set"
var text: String {
get{
    return "HelloWorld"
}
set{
    print(newValue)
}
}

// "又或者willGet和willSet"
var text: String = "HelloWorld" {
willSet{
    print("newValue:\(newValue)")
}
didSet{
    print("oldValue:\(oldValue)")
}
}
// "至于newValue和oldValue请自行查询"
~~~

~~~ruby
// "这是在某个UIViewController里面的成员变量"
private var iconImgView: UIImageView = {
	// "请注意这里不要调用self.iconImgView，原因你会懂的"
    let temp = UIImageView(image: UIImage(named: "imageName"))
    temp.frame = CGRectMake(0, 0, 100, 100)
    return temp
}()
~~~


## 尾随闭包（Trailing Closures）

Trailing闭包是一个书写在函数括号之外(之后)的闭包表达式，函数支持将其作为最后一个参数调用。
也就是说，如果你需要将一个很长的闭包表达式作为最后一个参数传递给函数，可以使用trailing闭包来增强函数的可读性。

比较正式点的写法大概是这样的:

~~~ruby
// "这是一个使用闭包的函数"
func someFunctionThatTakesAClosure(closure: () -> ()) {
    // "这里是函数本身的实现过程代码"
}
// "这是不使用尾随闭包的写法"
someFunctionThatTakesAClosure ({
    // "这里写闭包的实现过程"
})
// "这是使用尾随闭包的写法(注意和上面的区别)"
someFunctionThatTakesAClosure() {
    // "这里写尾随闭包的实现过程"
}
// "如果函数只需要闭包表达式一个参数，当使用尾随闭包时，甚至可以把()省略掉。"
someFunctionThatTakesAClosure {
    // "这里写尾随闭包的实现过程"
}
~~~

下面是一个具体的例子:

~~~ruby
func addTwoNumbers(num1: Int, num2: Int, CaluFunction: (Int, Int) -> Int) -> Int{
    return CaluFunction(num1, num2)
}
// "内联闭包形式，不使用尾随闭包"
var numReult1 = addTwoNumbers(2, num2: 3,CaluFunction: {(num1: Int, num2: Int) -> Int in
    return num1 + num2
})
// "使用尾随闭包"
var numReult2 = addTwoNumbers(3, num2: 4) {  $0 + $1 }
~~~


## 捕获值

HOHO~还记得它么，既然我们已经铺垫了这么多，现在是时候来把这个坑来填上了。

闭包可以在其定义的上下文中捕获常量或变量。 即使定义这些常量和变量的原域已经不存在，闭包仍然可以在闭包函数体内引用和修改这些值。

Swift最简单的闭包形式是嵌套函数，也就是定义在其他函数的函数体内的函数。 嵌套函数可以捕获其外部函数所有的参数以及定义的常量和变量。

先来个例子说明下捕获值的问题(PS:这个例子我是在网上找的，绝大多数的帖子都是复制的这个，相关资料也容易查找)，我就直接在代码中稍作解释，废话少说上代码:

~~~ruby
// "makeIncrementor返回类型为() -> Int。 这意味着其返回的是一个函数，而不是一个简单类型值。 该函数在每次调用时不接受参数只返回一个Int类型的值。"
func makeIncrementor(forIncrement amount: Int) -> () -> Int {
	// "makeIncrementor有一个Int类型的参数，其外部命名为forIncrement， 内部命名为amount，表示每次incrementor被调用时runningTotal将要增加的量。"
    var runningTotal = 0
    //incrementor函数用来执行实际的增加操作。 该函数简单地使runningTotal增加amount，并将其返回。
    func incrementor() -> Int {
        runningTotal += amount
        return runningTotal
    }
    // "请注意这里返回的是一个func"
    return incrementor
}

// "请注意，这里的ince是一个function，是makeIncrementor中返回的runningTotal，并非incrementor"
let ince = makeIncrementor(forIncrement: 3)
print(ince()) // "结果为 3"
~~~

然后针对返回的`func incrementor() -> Int` 稍作分析：

~~~ruby
/**
 1. "incrementor函数并没有获取任何参数，但是在函数体内访问了`runningTotal`和`amount`变量。这是因为其通过捕获在包含它的函数体内已经存在的`runningTotal`和`amount`变量而实现。"
 2. "由于没有修改amount变量，incrementor实际上捕获并存储了该变量的一个副本，而该副本随着incrementor一同被存储。"
 3. "然而，因为每次调用该函数的时候都会修改`runningTotal`的值，`incrementor`捕获了当前`runningTotal`变量的引用，而不是仅仅复制该变量的初始值。捕获一个引用保证了当`makeIncrementor`结束时候并不会消失，也保证了当下一次执行`incrementor`函数时，`runningTotal`可以继续增加。"
 */
func incrementor() -> Int {
    runningTotal += amount
    return runningTotal
}
~~~

PS:

	1. Swift会决定捕获引用还是拷贝值。
	2. 不需要标注`amount`或者`runningTotal`来声明在嵌入的`incrementor`函数中的使用方式。
	3. Swift同时也处理`runingTotal`变量的内存管理操作，如果不再被`incrementor`函数使用，则会被清除。

明白了这些以后，我们继续来测试，连续调用几次 ince() 试试看会有什么结果:

~~~ruby
let ince = makeIncrementor(forIncrement: 3)
print(ince()) // "结果为 3"
// "接着在我们之前的代码，后面继续添加这几行,看返回的结果"
ince()	// 6
ince()	// 9
ince()	// 12
~~~

可以看到的是，ince()返回的值是递增的，也意味着，它把之前的返回值`runningTotal`保存了下来。
`let ince = ...`定义了一个叫做`ince`的常量，该常量指向一个每次调用会加3的`incrementro`函数，这就是一个关键的地方。

让我们来继续：

~~~ruby
// "如果创建了另一个incrementor，其会有一个属于自己的独立的runningTotal变量的引用。 下面的例子中，inceNew捕获了一个新的runningTotal变量，该变量和incrementByTen中捕获的变量没有任何联系："
let inceNew = makeIncrementor(forIncrement: 10)
inceNew()	// 10
ince()	// 15(又加了一次)
~~~

PS:

	* 如果将闭包赋值给一个类实例的属性，并且该闭包通过指向该实例或其成员来捕获了该实例，那么将会创建一个在闭包和实例间的强引用环。
	* Swift 使用捕获列表来打破这种强引用环。更多信息，请参考 [闭包引起的循环强引用](http://www.yiibai.com/swift/strong_reference_cycles_for_closures.html)。

那么问题来了，不是说好的 `ince()` 和 `inceNew()`是`let`出来的常量么，怎么还能一直改变值呢？很简单，因为函数(func)和闭包(closures)都是 **`引用类型`** 。

当你指定一个函数或一个闭包常量/变量时，实际上是在设置该常量或变量是否为一个引用函数。在上面的例子中，ince是个闭包，ince指向的是恒定的，而不是封闭件本身的内容是恒定的。这也意味着，如果你分配一个封闭两种不同的常量或变量，这两个常量或变量将引用同一个闭包：

~~~ruby
let anotherInce = ince
anotherInce() // 18
~~~


## 非逃逸闭包(Nonescaping Closures)和swift中的block

等了这么久，也终于说到这个地方了。学闭包为了啥，反正我当时就是为了可以写一个block。。。可是swift中并没有OC中的block。没关系，我们可以用闭包来实现block相同的功能。

首先，要解释一下逃逸和非逃逸的闭包与block有啥关系。

一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，我们称该闭包从函数中逃逸。可以在参数名之前标注`@noescape`，用来指明这个闭包是不允许“逃逸”出这个函数的。将闭包标注`@noescape`能使编译器知道这个闭包的生命周期.

像数组中有提供的一个sort方法，函数中的参数就定义成了非逃逸闭包。

~~~ruby
public func sort(@noescape isOrderedBefore: (Self.Generator.Element, Self.Generator.Element) -> Bool) -> [Self.Generator.Element]
~~~

非逃逸的闭包目前在我的经验中用的比较少，更多的时候是用逃逸闭包。举个例子：很多启动异步操作的函数接受一个闭包参数作为 `completion handler`。这类函数会在异步操作开始之后立刻返回，但是闭包直到异步操作结束后才会被调用。在这种情况下，闭包需要“逃逸”出函数，因为闭包需要在函数返回之后被调用。

这里说明，非逃逸闭包和逃逸闭包讲的不是执行先后顺序。非逃逸是指你的闭包不能在函数外单独调用,只能在函数内部调用,函数调用完成后,那个闭包也就结束了。

~~~ruby
var completionHandler = {(finished: Bool) -> Void in}
func testDoSomething(finishedClosure: (Bool) -> Void) {
    // "进行一些操作，确认完成后，然后调用传入的闭包参数，并给闭包传参"
    finishedClosure(true)
}
// "调用函数"
testDoSomething { (done) in
	// "实现completionHandler"
    if done {
        print("task is done")
    }
}
~~~

## 未完待续，持续更新中



