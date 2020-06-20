---
layout:     post
title: '调用Html5plus API实现具有原生功能的web App之离线打包踩坑'
subtitle: '"路漫漫其修远兮，吾将上下而求索"'
date:       2019-05-20 06:52:52
author:     "is Q"
header-img: "img/post/2019/20190520/application.jpg"
catalog: true
mathjax: true
tags:
    - 5+ APP
    - HTML5plus
    - apk
    - 离线打包
    - 记录
---

> "路漫漫其修远兮，吾将上下而求索"

### 前言
这是在一个原有web项目上进行的一成封装，使其成为一个apk格式的安装包，还可以在你的web项目中调用Html5plus Api来实现一些原生功能，官方文档[html5+规范](http://www.html5plus.org/doc/zh_cn/webview.html)，请慎重入坑，主要是官方文档写的不太好以及几乎没有社区，只能自己一个一个的爬。  
以前我们的一个5+应用都是使用hbuilder进行云打包的，但是最近快要上线了，所以需要对接个推，接入个推就需要进行离线打包，因此有了这笔记！

### 下载H5+SDK

H5+应用离线打包的第一步，就是直接去官网下载相应版本的SDK：[HTML 5+ SDK 更新日志](<http://ask.dcloud.net.cn/article/103>)；最好下载最新版本的sdk，因为最新版本的sdk会修复以前出现的Bug（如果你不想自己踩太多的坑的话，我推荐下载最新版本的sdk包）。不要担心官方关于离线打包的文档和你使用的版本对应不上，然后造成自己离线打包出现重重阻碍，选择最新的sdk只会让你开头痛苦一下，后面你会比较顺利；不然后面你可能就是不断的打补丁，这个过程估计更痛苦，而且遇上SDK bug那么你还是得更新。

### 离线打包准备工作

如果自己的电脑上没有Android Studio（后面AS均代表该软件）的话记得去下载一个，我这里使用的是Android Studio3.4.1，先讲一下SDK中各文件夹的用途，如下图：

![one](/img/post/2019/20190520/one.png)

如图所示，我这里使用的是现在最新的sdk1.9.9.62327，其中目录结构如下：

1. 两个markdown文件都是说明使用该版本的SDK进行离线打包需要注意的一些东西，比如说某某功能不能和另一功能同时存在。
2. SDK就不用说了，就是真正的内容，其中的AndroidManifest.xml是安卓配置文件，assets是我们离线打包时需要放置app/src/main/目录下的，完整路径为app/src/main/assets；libs是一些功能依赖包文件（比如个推、分享等等）。
3. HBuilder-Hello和HBuilder-integerate-As都是离线打包demo，但是如果使用Android Studio进行离线打包的话请点击HBuilder-integerate-As，如果是使用Eclipse的话可以试一下另一版本（我没试过）。
4. 在uni-app中使用5+插件demo、UniPlugin-Hello-AS应该都是开发插件相关demo吧！由于此次没有用过这两个文件夹，因此它们的用处暂时不明白。
5. Feature-Android.xls是权限清单，在AndroidManifest.xml配置文件需要使用（调用相应的功能需要相应的权限），可以用来参考。

打开Android Studio，使用以下方式打开HBuilder-integerate-As这个demo：

![two](/img/post/2019/20190520/two.png)

有两种方式进行离线打包，我上面说的是我推荐的，还有一种的话直接使用start a new Android Studio project新建一个原生安卓项目，看一下官方文档后面操作几乎和我说的这个教程区别不大，就是有些文件你需要自己删除、添加，具体教程：[使用新版本5+SDK创建最简Android原生工程（Android studio）](<https://ask.dcloud.net.cn/article/13232>)，有很多文件都没有，踩的坑会比较多；我推荐的这种打包方式官方文档为：[Android平台本地（离线）打包指南 - Android Studio](<http://ask.dcloud.net.cn/article/508>)；按照官方文档的步骤的话，打开demo之后直接编译一遍，用来判断官方demo是否能够正常运行，如果demo不能运行下面的步骤就没有意义了，可以考虑换一个SDK包了。如果能正常运行，官方文档之后的第一步是引入依赖包，如果你是按照我推荐的这个方式来离线打包的，

![three](/img/post/2019/20190520/three.png)

这一步可以直接跳过，因为这个demo几乎已经集成所有的功能，依赖已经引入了，如果你再从新引入，可能会导致AndroidManifest.xml配置文件合并失败，所以信我的，别给自己找麻烦，后面功能正常之后再参考Feature-Android.xls引入相应依赖包和添加权限。  
官方文档的第二步分是配置应用权限

![four](/img/post/2019/20190520/four.png)

也可以直接跳过，demo也集成了，这就是我推荐的原因。  
第三步配置第三方库的数据

![five](/img/post/2019/20190520/five.png)

也可以先跳过等到引入程序之后编译成功再配置。

### 离线打包

如果你是直接离线打包并没有使用过云打包的话，那么你需要将你代码build后的文件使用hbuilder（x）进行离线打包（使用过云打包的应该看到过）如图：

![six](/img/post/2019/20190520/six.png)

离线打包在顶部的发行功能菜单中，如果你还只有一个dist文件夹（build的包）那么你需要使用hubuilderx先建一个空的5+app项目，这样你就会有一个manifest文件，然后你可以将你的dist文件夹下面的内容拷贝至该项目根目录下，然后点击上面的manifest.json文件可以可视化配置一些信息（启动图标、sdk配置等）。配置完成之后点击发行>原生app-本地打包>生成本地打包app资源，放置的位置为

![seven](/img/post/2019/20190520/seven.png)

我们的demo项目下的/app/src/main/assets/apps/XXXX/www/目录底下（其中XXXX为你的appId，注意：在AS左边的文件结构显示AcmApp.www代表AcmApp/www这是两个文件夹，不要把文件命名为AcmApp.www，不然上面就变成/app/src/main/assets/apps/XXXX.www了，这里是一个固定的格式appid/www，一定要按照这个格式，否则会导致编译失败），appId在hbuilderx可以查看manifest.json可视化界面或者直接查看manifest.json代码文件，一般你在使用hbuilderx离线打包时这一段XXXX/www/会自动生成，你只需要选择好放置的路径就行（你打开demo的/app/src/main/assets/apps路径下）。

到这里几乎完成一半了，现在要开始改改配置。

### 修改相应配置

在你（AS中）打开的demo项目中找到AndroidManifest.xml，修改步骤可以产看官方文档：

![eight](/img/post/2019/20190520/eight.png)

由于这里官方文档已经说的很详细了，所以我就不多说了，图标配置也说的很详细了，补充一点

1. icon.png代表桌面显示图标。
2. push.png代表推送显示图标（没试验过，因为我们应用icon和push使用的为一张图片）。
3. splash.png（应用启动页面）。

注意：这里的名字一定要按这个格式命名。

修改/app/src/main/assets/data/dcloud_control.xml文件，appid和manifest.json文件的id一样，appver和manifest.json文件的version.name一样，如下图：

![nine](/img/post/2019/20190520/nine.png)

最后修改/app/build.gradle文件，如下图：

![ten](/img/post/2019/20190520/ten.png)

其中applicationId配置为你的包名。

如果你是用另一种方法进行离线打包，那你需要按照这种方式一步步从demo里面拷贝文件并进行修改，同时我说的SDK/assets/data文件放置在你离线项目下这个位置就行：

![eleven](/img/post/2019/20190520/eleven.png)

但是没有apps文件？没办法你只能在从demo项目中拷贝过来，然后将H57991E95这个文件夹替换成你的离线包（Hbuilderx通过发行>原生app-本地打包>生成本地打包app资源生成的包）记住一定是这种格式appid/www/真正的资源文件，这也是我不推荐使用这种方式从零开始，如果你有一定的Android功底可以这样。

### AndroidManifest.xml重要配置项配置

不出意外上面的步骤已经让你成功离线打包了，而且应该是可以成功编译成功的，但是编译成功不代表你的应用所有功能都正常，这里有几个关于AndroidManifest.xml重要的配置项。

1. 个推的配置项

   首先得添加权限，检查一下你的项目是否添加：

   ```xml
   <!-- 配置在manifest节点中 -->
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.READ_PHONE_STATE" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
   <uses-permission android:name="android.permission.WAKE_LOCK" />
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
   <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
   <uses-permission android:name="android.permission.VIBRATE" />
   <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
   <uses-permission android:name="android.permission.CALL_PHONE" />
   <uses-permission android:name="getui.permission.GetuiService.你的app包名" />
   <permission
       android:name="getui.permission.GetuiService.你的app包名"
       android:protectionLevel="normal" />
   ```

   后面这两项server在同一设备上只能同时存在一个，因此配置了个推的就不要配置米推了。推送还需要做如下配置（还需要配置meta-data第三方库数据，可以参考官方文档，这里很简单）：

   ```xml
   !-- 配置在application节点中 -->
   <receiver android:name="io.dcloud.feature.apsGt.GTNotificationReceiver">
           <intent-filter>
               <action android:name="android.intent.action.BOOT_COMPLETED"/>
               <action android:name="你的包名.__CREATE_NOTIFICATION" />
               <action android:name="你的包名.__REMOVE_NOTIFICATION" />
               <action android:name="你的包名.__CLEAR_NOTIFICATION" />
               <action android:name="你的包名.__CLILK_NOTIFICATION" />
           </intent-filter>
       </receiver>
   ```

   上面标注包名的地方一定注意替换成你的包名（反向域名格式）。

2. 由于Android7.0+为了提高安全性对文件的权限控制更加严格，因此runtime的许多方法都会失效，我遇到的就有install（更新app）、打开文件（openFile），涉及到文件的应该都会有问题，而且还不会报错，这样根本让我无法定位，最后看到一篇博文才让我摆脱了这个问题，不然差一点就放弃了，在配置文件application节点中添加如下代码：

   ```xml
   <provider
           android:name="android.support.v4.content.FileProvider"
           android:authorities="你的包名.fileprovider"
           android:exported="false"
           android:grantUriPermissions="true">
           <meta-data
               android:name="android.support.FILE_PROVIDER_PATHS"
               android:resource="@xml/file_provider" />
       </provider>
   ```

   原本那篇博文还需要在src/main/res目路下添加xml/file_proviser.xml，但是我没添加一样可以，如果你按照上面配置runtime功能还是不正常可以尝试配置一下，博文链接：[解决 Android N 7.0 上 报错：android.os.FileUriExposedException](<https://blog.csdn.net/yy1300326388/article/details/52787853>)；

3. 还有一个值得注意的地方就是：有很多需要把它demo的包名替换成你自己的包名，但是有一些地方你是不能替换成你自己的包名的，一旦替换轻则某些功能异常，重则直接编译不通过，但是怎么区分呢？一般改动的时候你先把它原来的值删了，然后在按着它的值再敲一遍，不要复制，如果提示中有它以前那个值代表这是一个引用其他地方的文件，你不能删除；还有一种方法，配置引用的大部分依赖来自于/src/main/java文件夹，你在纠结是否把包名替换成你的包名的时候，去该路径找相应文件如com.HBuilder.integrate.SDK_WebView（我上面提过在AS文件结构.代表什么意思），则查看java文件夹下是否有com/Hbuilder/intergrate文件夹，并且还有SDK_WebView文件，如果有那么你就不能替换成你的包名。简单一点的方法就是，凡是配置文件里面的activity节点里的包名就要慎重替换，几个基本不能替换的：ActivityEntry、SDK_WebView、SDK_WebApp替换之后会导致直接不能通过编译，配置如下：

   ```xml
   <!-- 其中com.HBuilder.integrate你的参考你java文件夹下的目录结构，也许后面更新的结构有所改变 -->
   <activity
           android:name="com.HBuilder.integrate.ActivityEntry"
           android:configChanges="orientation|keyboardHidden"
           android:label="@string/app_name"
           android:launchMode="singleTask"
           android:screenOrientation="user"
           android:windowSoftInputMode="adjustResize">
       </activity>
       <activity
           android:name="com.HBuilder.integrate.SDK_WebApp"
           android:configChanges="orientation|keyboardHidden|screenSize|keyboard|navigation|mcc|mnc|fontScale"
           android:screenOrientation="user"
           android:theme="@style/DCloudTheme"> <!-- 离线配置沉浸式  SDK_WebApp  作为apk入口时  必须设置 SDK_WebApp   的主题为android:theme="@style/DCloudTheme" -->
       </activity>
       <activity
           android:name="com.HBuilder.integrate.SDK_WebView"
       android:configChanges="orientation|keyboardHidden|screenSize|keyboard|navigation|mcc|mnc|fontScale"
           android:screenOrientation="sensor"
           android:theme="@style/DCloudTheme"></activity>
   ```

   h5+应用离线打包的坑暂时还只踩到这里，以后遇到再过来补充。

最后添加几个有参考价值的官方文档，官方文档确实坑，但是这几个文档你还是可以当作字典一样做参考：

1. [5+ API使用的Android权限列表](<https://ask.dcloud.net.cn/article/100>)
2. [Android动态权限申请](<https://ask.dcloud.net.cn/article/35861>)
3. 离线打包关于Android7+文件操作异常问答：[Android7解决plus.runtime.openFile方法打开文件无响应问题（需本地打包并修改SDK）](<http://ask.dcloud.net.cn/article/12923>)
