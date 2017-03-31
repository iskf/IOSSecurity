# IOSSecurity
iOS安全，App加固保护原理
我们直接进图主题，就不说什么背景。

`我们可以从以下几个方面来保护我们的APP:`

### 字符串混淆
* 对应用程序中使用到的字符串进行加密，保证源码被逆向后不能看出字符串的直观含义。

### 类名、方法名混肴
* 对应用程序的方法名和方法体进行混淆，保证源码被逆向后很难明白它的真正功能。

### 程序结构混肴
* 对应用程序逻辑结构进行打乱混排，保证源码可读性降到最低。

### 反调试、反注入等一些主动保护策略
* 这是一些主动保护策略，增大破解者调试、分析APP的门槛。


#
#### 字符串加密 
* [字符串加密Demo](https://github.com/theKF/StringScurityDemo)<br>
字符串会暴露APP的很多关键信息，攻击者可以根据界面显示的字符串，快速找到相关逻辑的处理函数，从而进行分析破解。加密字符串可以增加攻击者阅读代码的难度以及根据字符串静态搜索的难度。<br><br>

比如一个APP中有如下的一些字符串定义在代码文件中：
![](https://github.com/theKF/IOSSecurity/blob/master/myfilexxx.png)<br>
经过加密后，代码文件变成如下的形式：<br>
![](https://github.com/theKF/IOSSecurity/blob/master/staicvoid.png)<br>
里面已经没有明文的字符串了，全是用byte的形式保存的，打包生成APP后，他们也就无法直观的看出实际内容了,这对破解者会造成巨大的难度：<br>
![](https://github.com/theKF/IOSSecurity/blob/master/tagscope.png)<br>

#
#### 符号混淆
* [方法名，变量名混淆Demo](https://github.com/theKF/SymbolConfusionDemo)<br>
符号混淆的中心思想是将类名、方法名、变量名替换为无意义符号，提高应用安全性；防止敏感符号被class-dump工具提取，防止IDA Pro等工具反编译后分析业务代码。
<br>
比如一款混淆后的APP,用IDA等工具打开，如下图所示:<br>
![](https://github.com/theKF/IOSSecurity/blob/master/entrypoint.png) <br>
“Labels”栏里，显示的这些符号，不管是类名还是方法名，谁也看不出来到底什么意思，这个函数到底是什么功能，就有点丈二和尚摸不着头脑的感觉，这就大大增加了破解者分析APP的难度。

#
#### 代码逻辑混淆
代码逻辑混淆有以下几个方面的含义：

对方法体进行混淆，保证源码被逆向后该部分的代码有很大的迷惑性，因为有一些垃圾代码的存在；

对应用程序逻辑结构进行打乱混排，保证源码可读性降到最低，这很容易把破解者带到沟里去；

它拥有和原始的代码一样的功能，这是最最关键的。

混淆前后的对比如下（左边是原始结构，右边是混淆后的结构）：
![](https://github.com/theKF/IOSSecurity/blob/master/xxxxxxxxx.png)<br>
下面以iOS APP中的main函数为例：
![](https://github.com/theKF/IOSSecurity/blob/master/initmain.png)<br>

它就只有一行有效代码，包含两个关键函数，已经算最简单的函数体了，混淆前的汇编代码如下：
![](https://github.com/theKF/IOSSecurity/blob/master/entrypoint.png)<br>

这里主要包含两个API的符号: NSStringFromClass、UIApplicationMain。其余就是一些消息发送以及内存管理的相关符号，但如果进行一定的代码逻辑混淆后，这个结构就会变得大不一样了。
![](https://github.com/theKF/IOSSecurity/blob/master/xxxxxxxxxyy.png)<br>
NSStringFromClass、UIApplicationMain这两个函数，逻辑结构已经变得非常复杂了，如果一个函数中，包含更多的代码的话，那这个结构将更加复杂，对破解者来说将是一个很耗时间、精力的过程，一般早早就会放弃分析了。

#
### URL编码加密
对程序中出现的URL进行编码加密，防止URL被静态分析。

#
### 网络传输数据加密
对客户端传输数据提供加密方案，防止通过网络接口的拦截获取数据。

#
### 主动保护策略
除了上面的一些被动保护方法，我们还可以加入一些主动的防护机制，比如反调试等。

iOS平台下的Anti-Debug方法一般有以下一些：

检查进程的状态是否为 P_TRACED。
调用ptrace请求来检查进程是否被调试。由于可能被攻击者绕过该方法的调用，在应用的多处增加ptrace函数会提高应用的安全性。
通过sysctl查看信息进程里的标记，判断自己是否正在被调试。sysctl是用以查询内核状态的接口，并允许具备相应权限的进程设置内核状态。




