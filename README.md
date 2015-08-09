#Adb-For-Test


通过 `adb 命令`实现简单的点击、滑动、长按等效果，可通过比例定位元素、通过元素定位（需要 Android 版本高于 4.0），用于`Android Test`，拥有 monkeyrunner 的绝大部分功能 

###2015.04.28
 * 重构 `python 版本`中的 adbUitils 模块，合并 `AppInfo`、`Action` 类至 `ADB`中
 
 	> 初始化方法：
 
	> adb = ADB() 或 adb = ADB("device_id")
 * 重构的目的是增加 python 版本对多设备的支持，通过传入 device_id，解决有多台设备连接 USB 时无法执行脚本的问题
 * `Element` 类中增加通过属性 `content-desc` 定位元素的方法
 
###Robotium
 * 如果想用于`Robotium`处理跨进程，需要修改部分代码，请参考[Adb-For-Robotium](https://github.com/gb112211/Adb-For-Robotium)

###为何写这么个东西？

 * 目前所做的工作是对 rom 的测试，所以基本上所有的测试环境都集中在 Android4.2、4.3、4.4平台，平台比较固定
 * Android 测试中最为熟悉的就是 adb 命令，很多功能都可通过adb命令去实现，因此几乎可以使用任何的编程语言去编写脚本调用 adb 命令，去实现对设备的操作
 * 将日常工作中封装的一些由 adb 命令完成的功能整合在一起，方便于使用
 * 作为自己学习 java、python 的一种实践

###优点

 * 分别采用两种语言（java、python）实现，需要的环境都极其的简单，不需要 root
 * 基本上都以 adb 命令完成功能，可从代码中剥离部分代码形成日常用的脚本（参考[AndroidTestScripts](https://github.com/gb112211/AndroidTestScripts)项目)
 * 由于是通过 adb 命令去发送事件，所以可跨应用进行测试，可用来扩展其他 Android 测试框架
 * 通过设备分辨率与坐标转换，可以方便的以屏幕比例作为参数来操作设备
 * 调用了 `uiautomator` 命令，Android4.0 以上，可通过界面元素进行定位，可获取界面内单个元素区域的坐标范围，以此截取图片（区别于截取整个屏幕）
 * 可采用截图对比全图、部分区域图片、获取当前界面某个元素、获取当前界面的 Activity 等各种属性，对测试结果进行判断

###缺点

 * 部分功能需要较高的 Android 版本，低版本的未去验证，所以局限性很大，纯当练手
 * 初学 java、python，代码写得比较烂
 * 很多地方未做异常处理
 * 暂时未写单元测试，所以使用时需要读者阅读具体的源码
 * 有疑问有吐槽请联系：Email: xuxu_1988@126.com  QQ:274925460

###需要的环境

 * 环境变量：需配置 ANDROID_HOME
 * python：python2.7，如果用到截图对比功能，需要安装 PIL 库，PIL 无法安装时可用[Pillow](http://www.pythonware.com/products/pil/index.htm "PIL地址")
 * java: jdk1.6 以上，不需要其他任何第三方 jar 包, jar 不可用时，可能是 jdk 版本问题，请自行编译

###如何使用

 * python：使用 setup.py install 安装，未去强制判断要求安装 PIL，使用 ImageUtils 时则需要安装
 * java：导入 `Adb-For-Test.jar` 即可

###简单例子

 * 在 TestDemo 目录下分别有 python、java 的例子，使用的 apk 是 ApiDemos.apk

 * 这里使用 python 写一个截取桌面应用图标的例子：

	```
	#coding=utf-8

	import os

	from adbUtils.utils.adbUtils import ADB
	from adbUtils.utils.imageUtils import ImageUtils
	from adbUtils.utils.element import Element
	
	#单个设备可不传入device_id
	adb = ADB(“device_id”)
	element = Element("device_id")

	#获取图片区域
	icon = element.getElementBoundsByClass("android.widget.TextView")[0]
	image = ImageUtils()
	#截取图片
	image.screenShot().subImage(icon).writeToFile(os.getcwd(), "image")

	#加载需要对比的目标图片
	#load = image.loadImage(os.getcwd() + "\\image.png")
	#print image.screenShot().subImage(icon).sameAs(load)
```