
# 搜狐视频iOS团队 Objective-C 编码规范
![](https://img.shields.io/badge/coverage-70%25-yellow.svg)

## 介绍

团队中长期以来存在各人不同的编码方式和习惯，导致代码中模块编码风格迥异，降低了可读性和维护性，经团队决定由[张科](https://github.com/GarfieldLover)、[李红力](https://github.com/lihongli528628)编写这份规范。

待定稿后务必遵守相关约定，这能使团队编码风格趋于一致，也有助于养成良好的代码习惯。


## 目录
* [命名](#命名)
  * [基本原则](#基本原则)
  * [命名类和协议](#命名类和协议)
  * [命名头文件](#命名头文件)
  * [命名方法](#命名方法)
  * [命名存取方法](#命名存取方法)
  * [命名委法](#命名委托)
  * [集合操作类方法](#集合操作类方法)
  * [命名属性和实例变量数](#命名属性和实例变量)
  * [命名常量](#命名常量)
  * [命名通知](#命名通知)
  * [图片命名](#图片命名)
* [注释](#注释)
  * [文件注释](#文件注释)
  * [代码注释](#代码注释)
* [代码格式](#代码格式)
  * [每一行的最大长度](#每一行的最大长度)
  * [空格](#空格)
  * [函数的书写](#函数的书写)
  * [函数调用](#函数调用)
  * [@public和@private标记符](#@public和@private标记符)
  * [协议（Protocols）](#协议（Protocols）)
  * [闭包（Blocks）](#闭包（Blocks）)
  * [字面量语法糖](#字面量语法糖)
  * [代码组织](#代码组织)
* [编码风格](#代码格式)
  * [不要使用new方法](#不要使用new方法)
  * [Public API要尽量简洁](#Public API要尽量简洁)
  * [#import和#include](##import和#include)
  * [引用框架的根头文件](#引用框架的根头文件)
  * [BOOL的使用](#BOOL的使用)
  * [init和dealloc](init和dealloc)
  * [Designated 和 Secondary 初始化方法](#Designated 和 Secondary 初始化方法)
  * [按照顺序释放资源](#按照顺序释放资源)
  * [nil检查](#nil检查)
  * [点语法的使用](#点语法的使用)
  * [Delegate要使用弱引用](#Delegate要使用弱引用)
  * [单例](#单例)
  * [KVO](#KVO)
  * [断言](#断言)
  * [Categories](#Categories)
  * [Pragma Mark](#pragma-mark)
* [设计模式](#设计模式)
  * [ReactiveCocoa+MVVM](#reactivecocoamvvm)
 

## 命名
###基本原则
####清晰
尽可能遵守 Apple 的命名约定，命名应该尽可能的清晰和简洁，但在Objective-C中，清晰比简洁更重要。

- 类名采用大驼峰（UpperCamelCase）
- 类成员、方法小驼峰（lowerCamelCase）
- 局部变量大小写首选小驼峰，也可使用小写下划线的形式（snake_case）
- C函数的命名用大驼峰

```
//清晰
insertObject:atIndex:

//不清晰，insert的对象类型和at的位置属性没有说明
insert:at:

//清晰
removeObjectAtIndex:

//不清晰，remove的对象类型没有说明，参数的作用没有说明
remove:
```
不要使用单词的简写，拼写出完整的单词：


```
// OK
ID, URL, JSON, WWW

// 糟糕
id, Url, json, www

//清晰
destinationSelection
setBackgroundColor:

//不清晰，不要使用简写
destSel
setBkgdColor:
```
####一致性
整个工程的命名风格要保持一致性，最好和苹果SDK的代码保持统一。不同类中完成相似功能的方法应该叫一样的名字，比如我们总是用count来返回集合的个数，不能在A类中使用count而在B类中使用getNumber。

####使用前缀
如果代码需要打包成Framework给别的工程使用，或者工程项目非常庞大，需要拆分成不同的模块，使用命名前缀是非常有用的。

- 前缀由大写的字母缩写组成，比如Cocoa中NS前缀代表Founation框架中的类，IB则代表Interface Builder框架。
- 可以在为类、协议、函数、常量以及typedef宏命名的时候使用前缀，但注意不要为成员变量或者方法使用前缀，因为他们本身就包含在类的命名空间内。
- 命名前缀的时候不要和苹果SDK框架冲突。

###命名类和协议

类名以大写字母开头，应该包含一个名词来表示它代表的对象类型，同时可以加上必要的前缀，比如NSString, NSDate, NSScanner, NSApplication等等。

而协议名称应该清晰地表示它所执行的行为，而且要和类名区别开来，所以通常使用ing词尾来命名一个协议，比如NSCopying,NSLocking。

###命名头文件

源码的头文件名应该清晰地暗示它的功能和包含的内容：

- 如果头文件内只定义了单个类或者协议，直接用类名或者协议名来命名头文件，比如NSLocale.h定义了NSLocale类。
- 如果头文件内定义了一系列的类、协议、类别，使用其中最主要的类名来命名头文件，比如NSString.h定义了NSString和NSMutableString。
- 每一个Framework都应该有一个和框架同名的头文件，包含了框架中所有公共类头文件的引用，比如Foundation.h
- Framework中有时候会实现在别的框架中类的类别扩展，这样的文件通常使用被扩展的框架名+Additions的方式来命名，比如NSBundleAdditions.h。

###命名方法

Objective-C的方法名通常都比较长，这是为了让程序有更好地可读性，按苹果的说法*“好的方法名应当可以以一个句子的形式朗读出来”*。

方法一般以小写字母打头，每一个后续的单词首字母大写，方法名中不应该有标点符号（*包括下划线*），有两个例外：

- 可以用一些通用的大写字母缩写打头方法，比如`PDF`,`TIFF`等。
- 可以用带下划线的前缀来命名私有方法或者类别中的方法。

如果方法表示让对象执行一个动作，使用动词打头来命名，注意不要使用`do`，`does`这种多余的关键字，动词本身的暗示就足够了：

```objective-c
//动词打头的方法表示让对象执行一个动作
- (void)invokeWithTarget:(id)target;
- (void)selectTabViewItem:(NSTabViewItem *)tabViewItem;
```

如果方法是为了获取对象的一个属性值，直接用属性名称来命名这个方法，注意不要添加`get`或者其他的动词前缀：

```objective-c
//正确，使用属性名来命名方法
- (NSSize)cellSize;

//错误，添加了多余的动词前缀
- (NSSize)calcCellSize;
- (NSSize)getCellSize;
```

对于有多个参数的方法，务必在每一个参数前都添加关键词，关键词应当清晰说明参数的作用：

```objective-c
//正确，保证每个参数都有关键词修饰
- (void)sendAction:(SEL)aSelector toObject:(id)anObject forAllCells:(BOOL)flag;

//错误，遗漏关键词
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;

//正确
- (id)viewWithTag:(NSInteger)aTag;

//错误，关键词的作用不清晰
- (id)taggedView:(int)aTag;
```

不要用`and`来连接两个参数，通常`and`用来表示方法执行了两个相对独立的操作（*从设计上来说，这时候应该拆分成两个独立的方法*）：

```objective-c
//错误，不要使用"and"来连接参数
- (int)runModalForDirectory:(NSString *)path andFile:(NSString *)name andTypes:(NSArray *)fileTypes;

//正确，使用"and"来表示两个相对独立的操作
- (BOOL)openFile:(NSString *)fullPath withApplication:(NSString *)appName andDeactivate:(BOOL)flag;
```
方法的参数命名也有一些需要注意的地方:
- 和方法名类似，参数的第一个字母小写，后面的每一个单词首字母大写
- 不要再方法名中使用类似`pointer`,`ptr`这样的字眼去表示指针，参数本身的类型足以说明
- 不要使用只有一两个字母的参数名
- 不要使用简写，拼出完整的单词

下面列举了一些常用参数名：

```objective-c
...action:(SEL)aSelector
...alignment:(int)mode
...atIndex:(int)index
...content:(NSRect)aRect
...doubleValue:(double)aDouble
...floatValue:(float)aFloat
...font:(NSFont *)fontObj
...frame:(NSRect)frameRect
...intValue:(int)anInt
...keyEquivalent:(NSString *)charCode
...length:(int)numBytes
...point:(NSPoint)aPoint
...stringValue:(NSString *)aString
...tag:(int)anInt
...target:(id)anObject
...title:(NSString *)aString
```

###命名存取方法

存取方法是指用来获取和设置类属性值的方法，属性的不同类型，对应着不同的存取方法规范：

```objective-c
//属性是一个名词时的存取方法范式
- (type)noun;
- (void)setNoun:(type)aNoun;
//栗子
- (NSString *)title;
- (void)setTitle:(NSString *)aTitle;

//属性是一个形容词时存取方法的范式
- (BOOL)isAdjective;
- (void)setAdjective:(BOOL)flag;
//栗子
- (BOOL)isEditable;
- (void)setEditable:(BOOL)flag;

//属性是一个动词时存取方法的范式
- (BOOL)verbObject;
- (void)setVerbObject:(BOOL)flag;
//栗子
- (BOOL)showsAlpha;
- (void)setShowsAlpha:(BOOL)flag;
```

命名存取方法时不要将动词转化为被动形式来使用：
```objective-c
//正确
- (void)setAcceptsGlyphInfo:(BOOL)flag;
- (BOOL)acceptsGlyphInfo;

//错误，不要使用动词的被动形式
- (void)setGlyphInfoAccepted:(BOOL)flag;
- (BOOL)glyphInfoAccepted;
```

可以使用`can`,`should`,`will`等词来协助表达存取方法的意思，但不要使用`do`,和`does`：
```objective-c
//正确
- (void)setCanHide:(BOOL)flag;
- (BOOL)canHide;
- (void)setShouldCloseDocument:(BOOL)flag;
- (BOOL)shouldCloseDocument;

//错误，不要使用"do"或者"does"
- (void)setDoesAcceptGlyphInfo:(BOOL)flag;
- (BOOL)doesAcceptGlyphInfo;
```

为什么Objective-C中不适用`get`前缀来表示属性获取方法？因为`get`在Objective-C中通常只用来表示从函数指针返回值的函数：
```objective-c
//三个参数都是作为函数的返回值来使用的，这样的函数名可以使用"get"前缀
- (void)getLineDash:(float *)pattern count:(int *)count phase:(float *)phase;
```

###命名委托

当特定的事件发生时，对象会触发它注册的委托方法。委托是Objective-C中常用的传递消息的方式。委托有它固定的命名范式。

一个委托方法的第一个参数是触发它的对象，第一个关键词是触发对象的类名，除非委托方法只有一个名为`sender`的参数：
```objective-c
//第一个关键词为触发委托的类名
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row;
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;

//当只有一个"sender"参数时可以省略类名
- (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;
```

根据委托方法触发的时机和目的，使用`should`,`will`,`did`等关键词
```objective-c
- (void)browserDidScroll:(NSBrowser *)sender;

- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)window;、

- (BOOL)windowShouldClose:(id)sender;
```

###集合操作类方法

有些对象管理着一系列其它对象或者元素的集合，需要使用类似“增删查改”的方法来对集合进行操作，这些方法的命名范式一般为：

```objective-c
//集合操作范式
- (void)addElement:(elementType)anObj;
- (void)removeElement:(elementType)anObj;
- (NSArray *)elements;

//栗子
- (void)addLayoutManager:(NSLayoutManager *)obj;
- (void)removeLayoutManager:(NSLayoutManager *)obj;
- (NSArray *)layoutManagers;
```

注意，如果返回的集合是无序的，使用`NSSet`来代替`NSArray`。如果需要将元素插入到特定的位置，使用类似于这样的命名：

```objective-c
- (void)insertLayoutManager:(NSLayoutManager *)obj atIndex:(int)index;
- (void)removeLayoutManagerAtIndex:(int)index;
```

如果管理的集合元素中有指向管理对象的指针，要设置成`weak`类型以防止引用循环。

下面是SDK中`NSWindow`类的集合操作方法：

```objective-c
- (void)addChildWindow:(NSWindow *)childWin ordered:(NSWindowOrderingMode)place;
- (void)removeChildWindow:(NSWindow *)childWin;
- (NSArray *)childWindows;
- (NSWindow *)parentWindow;
- (void)setParentWindow:(NSWindow *)window;
```

###命名属性和实例变量

属性和对象的存取方法相关联，属性的第一个字母小写，后续单词首字母大写，不必添加前缀。属性按功能命名成名词或者动词：

```objective-c
//名词属性
@property (strong) NSString *title;

//动词属性
@property (assign) BOOL showsAlpha;
```

属性也可以命名成形容词，这时候通常会指定一个带有`is`前缀的get方法来提高可读性：

```objective-c
@property (assign, getter=isEditable) BOOL editable;
```

命名实例变量，在变量名前加上`_`前缀（*有些有历史的代码会将`_`放在后面*），其它和命名属性一样：

```objective-c
@implementation MyClass {
    BOOL _showsTitle;
}
```

一般来说，类需要对使用者隐藏数据存储的细节，所以不要将实例方法定义成公共可访问的接口，可以使用`@private`，`@protected`前缀。


###命名常量

如果要定义一组相关的常量，尽量使用枚举类型（enumerations），枚举类型的命名规则和函数的命名规则相同。
建议使用 `NS_ENUM` 和 `NS_OPTIONS` 宏来定义枚举类型，参见官方的 [Adopting Modern Objective-C](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html) 一文：

```objective-c
//定义一个枚举
typedef NS_ENUM(NSInteger, NSMatrixMode) {
    NSRadioModeMatrix,
    NSHighlightModeMatrix,
    NSListModeMatrix,
    NSTrackModeMatrix
};
```

定义bit map：

```objective-c
typedef NS_OPTIONS(NSUInteger, NSWindowMask) {
    NSBorderlessWindowMask      = 0,
    NSTitledWindowMask          = 1 << 0,
    NSClosableWindowMask        = 1 << 1,
    NSMiniaturizableWindowMask  = 1 << 2,
    NSResizableWindowMask       = 1 << 3
};
```

使用`const`定义浮点型或者单个的整数型常量，如果要定义一组相关的整数常量，应该优先使用枚举。常量的命名规范和函数相同：

```objective-c
const float NSLightGray;
```

不要使用`#define`宏来定义常量，如果是整型常量，尽量使用枚举，浮点型常量，使用`const`定义。`#define`通常用来给编译器决定是否编译某块代码，比如常用的：

```objective-c
#ifdef DEBUG
```

注意到一般由编译器定义的宏会在前后都有一个`__`，比如*`__MACH__`*。

###命名通知

通知常用于在模块间传递消息，所以通知要尽可能地表示出发生的事件，通知的命名范式是：
	
	[触发通知的类名] + [Did | Will] + [动作] + Notification

栗子：

```objective-c
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```
###图片命名

1.用英文命名，不用拼音

2.每一部分用'-'分隔。分割的第一个首字母大写。

3.尽量表现内容+使用类型

4.尽量同一页面放置在同一个文件夹下

**推荐：**

```objc
Download-Progressbar-Highlighted@2x.png
Download-Progressbar-Normal@2x.png
```

##注释

读没有注释代码的痛苦你我都体会过，好的注释不仅能让人轻松读懂你的程序，还能提升代码的逼格。注意注释是为了让别人看懂，而不是仅仅你自己。

###文件注释

每一个文件都**必须**写文件注释，文件注释通常包含

- 文件所在模块
- 作者信息
- 历史版本信息
- 版权信息
- 文件包含的内容，作用

一段良好文件注释的栗子：

```objective-c
/*******************************************************************************
	Copyright (C), 2011-2013, Andrew Min Chang

	File name: 	AMCCommonLib.h
	Author:		Andrew Chang (Zhang Min) 
	E-mail:		LaplaceZhang@126.com
	
	Description: 	
			This file provide some covenient tool in calling library tools. One can easily include 
		library headers he wants by declaring the corresponding macros. 
			I hope this file is not only a header, but also a useful Linux library note.
			
	History:
		2012-??-??: On about come date around middle of Year 2012, file created as "commonLib.h"
		2012-08-20: Add shared memory library; add message queue.
		2012-08-21: Add socket library (local)
		2012-08-22: Add math library
		2012-08-23: Add socket library (internet)
		2012-08-24: Add daemon function
		2012-10-10: Change file name as "AMCCommonLib.h"
		2012-12-04: Add UDP support in AMC socket library
		2013-01-07: Add basic data type such as "sint8_t"
		2013-01-18: Add CFG_LIB_STR_NUM.
		2013-01-22: Add CFG_LIB_TIMER.
		2013-01-22: Remove CFG_LIB_DATA_TYPE because there is already AMCDataTypes.h

	Copyright information: 
			This file was intended to be under GPL protocol. However, I may use this library
		in my work as I am an employee. And my company may require me to keep it secret. 
		Therefore, this file is neither open source nor under GPL control. 
		
********************************************************************************/
```

*文件注释的格式通常不作要求，能清晰易读就可以了，但在整个工程中风格要统一。*

###代码注释

好的代码应该是“自解释”（self-documenting）的，但仍然需要详细的注释来说明参数的意义、返回值、功能以及可能的副作用。

方法、函数、类、协议、类别的定义都需要注释，推荐采用Apple的标准注释风格，好处是可以在引用的地方`alt+点击`自动弹出注释，非常方便。

有很多可以自动生成注释格式的插件，推荐使用[VVDocumenter](https://github.com/onevcat/VVDocumenter-Xcode)：

![Screenshot](https://raw.github.com/onevcat/VVDocumenter-Xcode/master/ScreenShot.gif)

Xcode8注释快捷键

![image description](https://raw.githubusercontent.com/SOHOVideo/Objective-C-Coding-Guidelines/master/707724-ed9730ab858c08a7.png)

一些良好的注释：

```objective-c
/**
 *  Create a new preconnector to replace the old one with given mac address.
 *  NOTICE: We DO NOT stop the old preconnector, so handle it by yourself.
 *
 *  @param type       Connect type the preconnector use.
 *  @param macAddress Preconnector's mac address.
 */
- (void)refreshConnectorWithConnectType:(IPCConnectType)type  Mac:(NSString *)macAddress;

/**
 *  Stop current preconnecting when application is going to background.
 */
-(void)stopRunning;

/**
 *  Get the COPY of cloud device with a given mac address.
 *
 *  @param macAddress Mac address of the device.
 *
 *  @return Instance of IPCCloudDevice.
 */
-(IPCCloudDevice *)getCloudDeviceWithMac:(NSString *)macAddress;

// A delegate for NSApplication to handle notifications about app
// launch and shutdown. Owned by the main app controller.
@interface MyAppDelegate : NSObject {
  ...
}
@end
```

协议、委托的注释要明确说明其被触发的条件：

```objective-c
/** Delegate - Sent when failed to init connection, like p2p failed. */
-(void)initConnectionDidFailed:(IPCConnectHandler *)handler;
```

如果在注释中要引用参数名或者方法函数名，使用`||`将参数或者方法括起来以避免歧义：

```objective-c
// Sometimes we need |count| to be less than zero.

// Remember to call |StringWithoutSpaces("foo bar baz")|
```

**定义在头文件里的接口方法、属性必须要有注释！**


##代码格式

###每一行的最大长度

同样的，在`Xcode > Preferences > Text Editing > Page guide at column:`中将最大行长设置为**80**，过长的一行代码将会导致可读性问题。

###空格

类方法声明在方法类型与返回类型之间要有空格。

```
// 糟糕
-(void)methodName:(NSString *)string;

// OK
- (void)methodName:(NSString *)string;
```

条件判断的括号内侧不应有空格。


```
// 糟糕
if ( a < b ) {
    // something
}

// OK
if (a < b) {
    // something
}
```

关系运算符（如 >=、!=）和逻辑运算符（如 &&、||）两边要有空格。


```
// OK
(someValue > 100)? YES : NO

// OK
(items)?: @[]
```

二元算数运算符两侧是否加空格不确定，根据情况自己定。一元运算符与操作数之前没有空格。


###函数的书写

一个典型的Objective-C函数应该是这样的：

```objective-c
- (void)writeVideoFrameWithData:(NSData *)frameData timeStamp:(int)timeStamp {
    ...
}
```

在`-`和`(void)`之间应该有一个空格，第一个大括号`{`的位置在函数所在行的末尾，同样应该有一个空格。（_我司的C语言规范要求是第一个大括号单独占一行，但考虑到OC较长的函数名和苹果SDK代码的风格，还是将大括号放在行末。_）

如果一个函数有特别多的参数或者名称很长，应该将其按照`:`来对齐分行显示：

```objective-c
-(id)initWithModel:(IPCModle)model
       ConnectType:(IPCConnectType)connectType
        Resolution:(IPCResolution)resolution
          AuthName:(NSString *)authName
          Password:(NSString *)password
               MAC:(NSString *)mac
              AzIp:(NSString *)az_ip
             AzDns:(NSString *)az_dns
             Token:(NSString *)token
             Email:(NSString *)email
          Delegate:(id<IPCConnectHandlerDelegate>)delegate;
```

在分行时，如果第一段名称过短，后续名称可以以Tab的长度（4个空格）为单位进行缩进：

```objective-c
- (void)short:(GTMFoo *)theFoo
        longKeyword:(NSRect)theRect
  evenLongerKeyword:(float)theInterval
              error:(NSError **)theError {
    ...
}
```
###函数调用

函数调用的格式和书写差不多，可以按照函数的长短来选择写在一行或者分成多行：

```objective-c
//写在一行
[myObject doFooWith:arg1 name:arg2 error:arg3];

//分行写，按照':'对齐
[myObject doFooWith:arg1
               name:arg2
              error:arg3];

//第一段名称过短的话后续可以进行缩进
[myObj short:arg1
          longKeyword:arg2
    evenLongerKeyword:arg3
                error:arg4];
```

以下写法是错误的：

```objective-c
//错误，要么写在一行，要么全部分行
[myObject doFooWith:arg1 name:arg2
              error:arg3];
[myObject doFooWith:arg1
               name:arg2 error:arg3];

//错误，按照':'来对齐，而不是关键字
[myObject doFooWith:arg1
          name:arg2
          error:arg3];
```

###@public和@private标记符

@public和@private标记符应该以**一个空格**来进行缩进：

```objective-c
@interface MyClass : NSObject {
 @public
  ...
 @private
  ...
}
@end
```

###协议（Protocols）

在书写协议的时候注意用`<>`括起来的协议和类型名之间是没有空格的，比如`IPCConnectHandler()<IPCPreconnectorDelegate>`,这个规则适用所有书写协议的地方，包括函数声明、类声明、实例变量等等：

```objective-c
@interface MyProtocoledClass : NSObject<NSWindowDelegate> {
 @private
    id<MyFancyDelegate> _delegate;
}

- (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
@end
```

###闭包（Blocks）

根据block的长度，有不同的书写规则：

- 较短的block可以写在一行内。
- 如果分行显示的话，block的右括号`}`应该和调用block那行代码的第一个非空字符对齐。
- block内的代码采用**4个空格**的缩进。
- 如果block过于庞大，应该单独声明成一个变量来使用。
- `^`和`(`之间，`^`和`{`之间都没有空格，参数列表的右括号`)`和`{`之间有一个空格。

```objective-c
//较短的block写在一行内
[operation setCompletionBlock:^{ [self onOperationDone]; }];

//分行书写的block，内部使用4空格缩进
[operation setCompletionBlock:^{
    [self.delegate newDataAvailable];
}];

//使用C语言API调用的block遵循同样的书写规则
dispatch_async(_fileIOQueue, ^{
    NSString* path = [self sessionFilePath];
    if (path) {
      // ...
    }
});

//较长的block关键字可以缩进后在新行书写，注意block的右括号'}'和调用block那行代码的第一个非空字符对齐
[[SessionService sharedService]
    loadWindowWithCompletionBlock:^(SessionWindow *window) {
        if (window) {
          [self windowDidLoad:window];
        } else {
          [self errorLoadingWindow];
        }
    }];

//较长的block参数列表同样可以缩进后在新行书写
[[SessionService sharedService]
    loadWindowWithCompletionBlock:
        ^(SessionWindow *window) {
            if (window) {
              [self windowDidLoad:window];
            } else {
              [self errorLoadingWindow];
            }
        }];

//庞大的block应该单独定义成变量使用
void (^largeBlock)(void) = ^{
    // ...
};
[_operationQueue addOperationWithBlock:largeBlock];

//在一个调用中使用多个block，注意到他们不是像函数那样通过':'对齐的，而是同时进行了4个空格的缩进
[myObject doSomethingWith:arg1
    firstBlock:^(Foo *a) {
        // ...
    }
    secondBlock:^(Bar *b) {
        // ...
    }];
```

###字面量语法糖

应该使用可读性更好的语法糖来构造`NSArray`，`NSDictionary`等数据结构，避免使用冗长的`alloc`,`init`方法。

如果构造代码写在一行，需要在括号两端留有一个空格，使得被构造的元素于与构造语法区分开来：

```objective-c
//正确，在语法糖的"[]"或者"{}"两端留有空格
NSArray *array = @[ [foo description], @"Another String", [bar description] ];
NSDictionary *dict = @{ NSForegroundColorAttributeName : [NSColor redColor] };

//不正确，不留有空格降低了可读性
NSArray* array = @[[foo description], [bar description]];
NSDictionary* dict = @{NSForegroundColorAttributeName: [NSColor redColor]};
```

如果构造代码不写在一行内，构造元素需要使用**两个空格**来进行缩进，右括号`]`或者`}`写在新的一行，并且与调用语法糖那行代码的第一个非空字符对齐：

```objective-c
NSArray *array = @[
  @"This",
  @"is",
  @"an",
  @"array"
];

NSDictionary *dictionary = @{
  NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],
  NSForegroundColorAttributeName : fontColor
};
```

构造字典时，字典的Key和Value与中间的冒号`:`都要留有一个空格，多行书写时，也可以将Value对齐：

```objective-c
//正确，冒号':'前后留有一个空格
NSDictionary *option1 = @{
  NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],
  NSForegroundColorAttributeName : fontColor
};

//正确，按照Value来对齐
NSDictionary *option2 = @{
  NSFontAttributeName :            [NSFont fontWithName:@"Arial" size:12],
  NSForegroundColorAttributeName : fontColor
};

//错误，冒号前应该有一个空格
NSDictionary *wrong = @{
  AKey:       @"b",
  BLongerKey: @"c",
};

//错误，每一个元素要么单独成为一行，要么全部写在一行内
NSDictionary *alsoWrong= @{ AKey : @"a",
                            BLongerKey : @"b" };

//错误，在冒号前只能有一个空格，冒号后才可以考虑按照Value对齐
NSDictionary *stillWrong = @{
  AKey       : @"b",
  BLongerKey : @"c",
};
```

###代码组织
- 函数长度（行数）不应超过2/3屏幕，禁止超过70行。
- 单个文件方法数不应超过30个
- 不要按类别排序（如把IBAction放在一块），应按任务把相关的组合在一起
- 禁止出现超过两层循环的代码，用函数或block替代。


```
// 为了简化示例，没有错误处理，并使用了伪代码

// 糟糕的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    Task *aTask;
    if ([path isURL]) {
        if ([fileManager isWritableFileAtPath:path]) {
            if (![taskManager hasTaskWithPath:path]) {
                aTask = [[Task alloc] initWithPath:path];
            }
            else {
                return nil;
            }
        }
        else {
            return nil;
        }
    }
    else {
        return nil;
    }
    return aTask;
}

// 改写的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    if (![path isURL]) {
        return nil;
    }

    if (![fileManager isWritableFileAtPath:path]) {
        return nil;
    }

    if ([taskManager hasTaskWithPath:path]) {
        return nil;
    }

    Task *aTask = [[Task alloc] initWithPath:path];
    return aTask;
}
```


##编码风格

每个人都有自己的编码风格，这里总结了一些比较好的Cocoa编程风格和注意点。

###不要使用new方法

尽管很多时候能用`new`代替`alloc init`方法，但这可能会导致调试内存时出现不可预料的问题。Cocoa的规范就是使用`alloc init`方法，使用`new`会让一些读者困惑。

###Public API要尽量简洁

共有接口要设计的简洁，满足核心的功能需求就可以了。不要设计很少会被用到，但是参数极其复杂的API。如果要定义复杂的方法，使用类别或者类扩展。

###\#import和\#include

`#import`是Cocoa中常用的引用头文件的方式，它能自动防止重复引用文件，什么时候使用`#import`，什么时候使用`#include`呢？

- 当引用的是一个Objective-C或者Objective-C++的头文件时，使用`#import`
- 当引用的是一个C或者C++的头文件时，使用`#include`，这时必须要保证被引用的文件提供了保护域（#define guard）。

为什么不全部使用`#import`呢？主要是为了保证代码在不同平台间共享时不出现问题。

###引用框架的根头文件

上面提到过，每一个框架都会有一个和框架同名的头文件，它包含了框架内接口的所有引用，在使用框架的时候，应该直接引用这个根头文件，而不是其它子模块的头文件，即使是你只用到了其中的一小部分，编译器会自动完成优化的。

```objective-c
//正确，引用根头文件
#import <Foundation/Foundation.h>

//错误，不要单独引用框架内的其它头文件
#import <Foundation/NSArray.h>
#import <Foundation/NSString.h>
```

###BOOL的使用

BOOL在Objective-C中被定义为`signed char`类型，这意味着一个BOOL类型的变量不仅仅可以表示`YES`(1)和`NO`(0)两个值，所以永远**不要**将BOOL类型变量直接和`YES`比较：

```objective-c
//错误，无法确定|great|的值是否是YES(1)，不要将BOOL值直接与YES比较
BOOL great = [foo isGreat];
if (great == YES)
  // ...be great!

//正确
BOOL great = [foo isGreat];
if (great)
  // ...be great!
```

同样的，也不要将其它类型的值作为BOOL来返回，这种情况下，BOOL变量只会取值的最后一个字节来赋值，这样很可能会取到0（NO）。但是，一些逻辑操作符比如`&&`,`||`,`!`的返回是可以直接赋给BOOL的：

```objective-c
//错误，不要将其它类型转化为BOOL返回
- (BOOL)isBold {
  return [self fontTraits] & NSFontBoldTrait;
}
- (BOOL)isValid {
  return [self stringValue];
}

//正确
- (BOOL)isBold {
  return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
}

//正确，逻辑操作符可以直接转化为BOOL
- (BOOL)isValid {
  return [self stringValue] != nil;
}
- (BOOL)isEnabled {
  return [self isValid] && [self isBold];
}
```

另外BOOL类型可以和`_Bool`,`bool`相互转化，但是**不能**和`Boolean`转化。

###init和dealloc

推荐的代码组织方式是将 dealloc 方法放在实现文件的最前面（直接在 @synthesize 以及 @dynamic 之后），init 应该跟在 dealloc 方法后面。

当`init``dealloc`方法被执行时，类的运行时环境不是处于正常状态的，使用存取方法访问变量可能会导致不可预料的结果，因此应当在这两个方法内直接访问实例变量。

```objective-c
//正确，直接访问实例变量
- (instancetype)init {
  self = [super init];
  if (self) {
    _bar = [[NSMutableString alloc] init];
  }
  return self;
}
- (void)dealloc {
  [_bar release];
  [super dealloc];
}

//错误，不要通过存取方法访问
- (instancetype)init {
  self = [super init];
  if (self) {
    self.bar = [NSMutableString string];
  }
  return self;
}
- (void)dealloc {
  self.bar = nil;
  [super dealloc];
}
```

###Designated 和 Secondary 初始化方法

Objective-C 有指定初始化方法(designated initializer)和间接(secondary initializer)初始化方法的观念。 designated 初始化方法是提供所有的参数，secondary 初始化方法是一个或多个，并且提供一个或者更多的默认参数来调用 designated 初始化的初始化方法。


```
@implementation ZOCEvent

- (instancetype)initWithTitle:(NSString *)title
                         date:(NSDate *)date
                     location:(CLLocation *)location
{
    self = [super init];
    if (self) {
        _title    = title;
        _date     = date;
        _location = location;
    }
    return self;
}

- (instancetype)initWithTitle:(NSString *)title
                         date:(NSDate *)date
{
    return [self initWithTitle:title date:date location:nil];
}

- (instancetype)initWithTitle:(NSString *)title
{
    return [self initWithTitle:title date:[NSDate date] location:nil];
}

@end
```

initWithTitle: date: location: 就是 designated 初始化方法，另外的两个是 secondary 初始化方法。因为它们仅仅是调用类实现的 designated 初始化方法

###按照顺序释放资源

在类或者Controller的生命周期结束时，往往需要做一些扫尾工作，比如释放资源，停止线程等，这些扫尾工作的释放顺序应当与它们的初始化或者定义的顺序保持一致。这样做是为了方便调试时寻找错误，也能防止遗漏。

###保证NSObject在赋值时被复制

在传递或者赋值时应当保证是以复制（copy）的方式进行的，这样可以防止在不知情的情况下String的值被其它对象修改。

```objective-c
- (void)setFoo:(NSString *)aFoo {
  _foo = [aFoo copy];
}
```

###nil检查

因为在Objective-C中向nil对象发送命令是不会抛出异常或者导致崩溃的，只是完全的“什么都不干”，所以，只在程序中使用nil来做逻辑上的检查。

另外，不要使用诸如`nil == Object`或者`Object == nil`的形式来判断。

```objective-c
//正确，直接判断
if (!objc) {
	...	
}

//错误，不要使用nil == Object的形式
if (nil == objc) {
	...	
}
```

###点语法的使用

不要用点分语法来调用方法，只用来访问属性。这样是为了防止代码可读性问题。

```objective-c
//正确，使用点分语法访问属性
NSString *oldName = myObject.name;
myObject.name = @"Alice";

//错误，不要用点分语法调用方法
NSArray *array = [NSArray arrayWithObject:@"hello"];
NSUInteger numberOfItems = array.count;
array.release;
```

###Delegate要使用弱引用

一个类的Delegate对象通常还引用着类本身，这样很容易造成引用循环的问题，所以类的Delegate属性要设置为弱引用。

```objective-c
/** delegate */
@property (nonatomic, weak) id <IPCConnectHandlerDelegate> delegate;
```

###单例

如果可能，请尽量避免使用单例而是依赖注入。 然而，如果一定要用，请使用一个线程安全的模式来创建共享的实例。对于 GCD，用 dispatch_once() 函数就可以咯。


```
+ (instancetype)sharedInstance
{
   static id sharedInstance = nil;
   static dispatch_once_t onceToken = 0;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });
   return sharedInstance;
}
```
###KVO
KVO触发机制:一个对象(观察者),检测另一个对象(被观察者)的某属性是否发生变化,若被监测的属性发生了更改,会触发观察者的一个方法(方法名固定,类似代理方法)

- 注册观察者(为被观察这指定观察者以及被观察者属性)
- 实现回调方法
- 触发回调方法
- 移除观察者

一般KVO奔溃的原因:

- 被观察的对象销毁掉了(被观察的对象是一个局部变量)
- 观察者被释放掉了,但是没有移除监听(如模态推出,push,pop等)
- 注册的监听没有移除掉,又重新注册了一遍监听


###断言

你的方法可能要求一些参数来满足特定的条件（比如不能为nil），在这种情况下最好使用 NSParameterAssert() 来断言条件是否成立或是抛出一个异常。


###Categories
虽然我们知道这样写很丑, 但是我们应该要在我们的 category 方法前加上自己的小写前缀以及下划线，比如- (id)zoc_myCategoryMethod。 这种实践同样被苹果推荐。

这是非常必要的。因为如果在扩展的 category 或者其他 category 里面已经使用了同样的方法名，会导致不可预计的后果。实际上，实际被调用的是最后被加载的那个 category 中方法的实现。

一个好的实践是在 category 名中使用前缀。

** 例子 **


```
@interface NSDate (ZOCTimeExtensions)
- (NSString *)zoc_timeAgoShort;
@end
** 不要这样 **

@interface NSDate (ZOCTimeExtensions)
- (NSString *)timeAgoShort;
@end
```

分类可以用来在头文件中定义一组功能相似的方法。这是在 Apple的 Framework 也很常见的一个实践（下面例子的取自NSDate 头文件）。我们也强烈建议在自己的代码中这样使用。

我们的经验是，创建一组分类对以后的重构十分有帮助。一个类的接口增加的时候，可能意味着你的类做了太多事情，违背了类的单一功能原则。

之前创造的方法分组可以用来更好地进行不同功能的表示，并且把类打破在更多自我包含的组成部分里。


```
@interface NSDate : NSObject <NSCopying, NSSecureCoding>

@property (readonly) NSTimeInterval timeIntervalSinceReferenceDate;

@end

@interface NSDate (NSDateCreation)

+ (instancetype)date;
+ (instancetype)dateWithTimeIntervalSinceNow:(NSTimeInterval)secs;
+ (instancetype)dateWithTimeIntervalSinceReferenceDate:(NSTimeInterval)ti;
+ (instancetype)dateWithTimeIntervalSince1970:(NSTimeInterval)secs;
+ (instancetype)dateWithTimeInterval:(NSTimeInterval)secsToBeAdded sinceDate:(NSDate *)date;
// ...
@end
```

###Pragma Mark


`#pragma mark`是一个在类内部组织代码并且帮助你分组方法实现的好办法。 我们建议使用 `#pragma mark` - 来分离:

不同功能组的方法
protocols 的实现
对父类方法的重写
- (void)dealloc { /* ... */ }
- (instancetype)init { /* ... */ }

`#pragma mark` - View Lifecycle （View 的生命周期）

- (void)viewDidLoad { /* ... */ }
- (void)viewWillAppear:(BOOL)animated { /* ... */ }
- (void)didReceiveMemoryWarning { /* ... */ }

##设计模式
###ReactiveCocoa+MVVM
推荐使用ReactiveCocoa+MVVM设计代码架构，只有代码架构上清晰合理了上述规则才能锦上添花。

具体使用方法参考下列文章。

[ReactiveCocoa 和 MVVM 入门](http://yulingtianxia.com/blog/2015/05/21/ReactiveCocoa-and-MVVM-an-Introduction/)

[MVVM With ReactiveCocoa](http://blog.leichunfeng.com/blog/2016/02/27/mvvm-with-reactivecocoa/)




---



关于这个编程语言的所有规范，如果这里没有写到，那就在苹果的文档里： 

* [Cocoa 编码指南][Introduction_3]
* [iOS 应用编程指南][Introduction_4]

[Introduction_3]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

[Introduction_4]:http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html
