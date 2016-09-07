# android开发内容总结

## 开篇
android开发内容，分为下边5个方面，分别为：

* 开发框架
* 代码组织架构
* 代码规范
* 热修复/插件化
* 持续集成 

暂时这么确定，需要更改的时候再添加。

## 开发框架
开发框架分六个方面，分别为:

* 网络请求框架
* 图片加载框架
* 数据库
* View的依赖注入
* 事件总线
* 内存泄露监测
* 权限请求以及处理（针对Android6.0以上机器适配）
* 大全套系列--重型框架，单独说明
* 第三方服务

使用原则，考虑当前项目的需求，尽可能使用轻量级框架。

### 网络请求框架
网络请求框架主要有下面几种：

* Retrofit:Square出品，在OkHttp基础上封装的请求，使用接口定义请求参数，比较适用于RESTful风格的api接口，通过注解进行配置，当项目中添加OkHttp框架的时候自动调用OkHttp框架处理请求，支持不同的json库（例如Gson，fastJson）进行javabean映射。几个使用教程如下：
	* https://github.com/hehonghui/android-tech-frontier/tree/master/issue-7/Retrofit%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97
	* 官网文档http://square.github.io/retrofit/
	* http://blog.csdn.net/jiangxuqaz/article/details/50759239
	
* OkHttp：Square出品，基于http协议封装的一套请求客户端，虽然它也可以开线程，但根本上它更偏向真正的请求，跟HttpClient, HttpUrlConnection的职责是一样的。封装的话，就类似于volley框架，需要参考下面两个封装：
	* http://blog.csdn.net/lmj623565791/article/details/49734867--【张鸿洋的博客】
	* https://github.com/ZhaoKaiQiang/OkHttpPlus--OkHttpPlus赵凯强的封装
	
* AsyncHttpClient：基于HttpClient的异步请求库，已经Copy了一份HttpClient的源码过来，还需要自己封装请求过程，相对要麻烦一些。同OkHttp一样，但是它是基于HttpClient的封装。使用教程直接看官方文档即可：
	* http://loopj.com/android-async-http/ 官方文档
	
* Volley：不支持 post 大数据，所以不适合上传文件。设计初衷是为频繁的、数据量小的网络请求而生，文档不全面也是一种限制。（我暂时没使用过，没有合适的文档推荐）

* NoHttp：今年新鲜出炉的Http框架，可以使用OkHttp作为底层框架，需要学习和研究。
	* http://www.nohttp.net/ 官方文档

*总结*：倾向于使用Retrofit编写网络请求，也可以尝试新的NoHttp框架。

参考文章：

* https://zhuanlan.zhihu.com/p/21879931 Stormzhang出品，值得信任
* http://stackoverflow.com/questions/16902716/comparison-of-android-networking-libraries-okhttp-retrofit-volley# stackOverflow的问题回答

### 图片加载框架
图片加载框架主要是下面几种：

* Universal-Image-Loader：比较古老的图片加载框架，带有多种缓存策略，但是目前缺乏维护，加上框架比较大，不再建议使用。
	* http://smallwoniu.blog.51cto.com/3911954/1336194 使用教程
	* http://blog.csdn.net/vipzjyno1/article/details/23206387 使用教程2
	* http://blog.csdn.net/xiaanming/article/details/26810303 使用介绍和解析，多篇文章

* Glide：Google官方推荐，兼容性较好，可以看做是picasso框架的改进版，支持Gif图片加载，推荐使用。
	* http://mrfu.me/2016/02/27/Glide_Getting_Started/ Glide教程，完整版，全系列多文章
	* http://www.voidcn.com/blog/u013278099/article/p-5005576.html 简单教程
	* 我自己总结了一部分Glide使用的内容，待补充
	
* Picasso：Square最轻量级的图片加载框架，方法数最少，二级缓存，编写情况和Glide相似，差别不大。
	* http://square.github.io/picasso/ 官方文档讲的最详细，值得一看
	
* Fresco：Facebook开源的图片加载框架，相对来说是最重的框架之一，支持WebP类型的图片，三级缓存模式，功能最齐全，但是占用apk空间最大，谨慎使用。
	* http://fresco-cn.org/ 中文官网
	* http://www.jianshu.com/p/061d5ab2f9d2 简明教程

* 其他，volley也有图片加载的功能，但是相比上面的框架来说功能很弱，不建议使用；其他的大型框架中也有此功能，后续编写。

*总结*：如果apk不在乎体积，优先考虑Fresco框架，否则优先使用Glide框架。

参考文章：

* http://www.jianshu.com/p/3ac30878c72c 几个框架的对比
* http://stackoverflow.com/questions/29363321/picasso-v-s-imageloader-v-s-fresco-vs-glide stackOverflow上的对比
* http://blog.csdn.net/a910626/article/details/50688637 比较全面的对比

### 数据库
数据库方面的框架有下面几种：

* OrmLite：基于注解和反射的的方式,导致ormlite性能有着一定的损失，但是文档较全面，社区活跃，有好的维护，使用简单，易上手。适合对性能要求不高的应用。
	* http://ormlite.com/ 官方文档
	* http://www.cnblogs.com/alexthecoder/p/4277758.html 使用教程一篇

* LitePal：没有使用过，可能比较陈旧
	* http://blog.csdn.net/guolin_blog/article/details/38083103 郭霖的博客，整个系列讲数据库如何使用

* GreenDao：效率较高，操作实体灵活。原理不是根据反射进行数据库的各项操作，而是一开始就人工生成业务需要的Model和DAO文件，业务中可以直接调用相应的DAO文件进行数据库的增删改查操作，从而避免了因反射带来的性能损耗和效率低下，带来的后果是学习成本较高。
	* http://greenrobot.org/greendao/ 官网文档
	* http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0727/3223.html 使用教程

* LiteOrm：没有使用过，不太有名，社区不活跃
	* https://github.com/litesuits/android-lite-orm 官方文档
	
* Realm：重型的移动开源数据库，底层基于c/c++实现，Android/iOS均有，项目比较大，但是性能最好，学习成本中等，使用需要慎重。
	* https://realm.io/docs/java/0.71.0/ 官方文档，内容齐全
	* https://bng86.gitbooks.io/android-third-party-/content/realm.html 港台开发者出品，可以借鉴
	
* 其他：包含xUtils等重型框架使用的数据库ORM工具，后续进行介绍。

*总结*：强调性能的话，是哟个GreenDao或者Realm，如果对性能要求不高，优先使用OrmLite

参考文章：

* https://segmentfault.com/q/1010000002408774 论坛讨论贴
* https://bng86.gitbooks.io/android-third-party-/content/index.html 开发者的第三方库总结

### View的依赖注入

* ButterKnife:JakeWarton出品，使用最广泛的View注入框架，简单粗暴, 好用简洁。
	* http://jakewharton.github.io/butterknife/ 官方文档
	* http://www.jianshu.com/p/9ad21e548b69 使用教程，文章比较多，可以参考别的
	
* Dagger2：Dagger1的升级版本，更加快速，使用起来不如ButterKnife容易编写。
	* http://google.github.io/dagger/users-guide 官方文档
	* http://gold.xitu.io/entry/578482316be3ff0042a9575b 掘金出品文章

* RoboGuice：问题比较多，暂时不考虑

* Android Annotations：需要学习

* 其他：xUtils等重型框架后续进行介绍

*总结*：View的依赖注入框架，优先选择ButterKnife。 

### 事件总线

* EventBus：最简洁的事件总线库，使用简单。典型的发布/订阅模式，开销小，代码优雅。拓展能力和使用场景更加丰富。
	* https://github.com/greenrobot/EventBus 使用直接看官方文档即可，比较简单
	
* Otto：基于反射，性能稍差，使用同样比较简洁。
	* http://square.github.io/otto/ 直接参考官方文档
	
* RxJava+RxAndroid：全新的Android开发方式，也是发布订阅模式的典型应用，但是学习成本较高。

*总结*：优先选择EventBus框架，但是需要注意一对多状态下，如何在不同的调用方法中添加对消息的区分！

参考文章：

* http://blog.zhaiyifan.cn/2015/08/17/EventBus:otto:LocalBroadcast%E7%9A%84%E9%80%89%E6%8B%A9/ 使用对比
* http://www.androidchina.net/2328.html 奇技淫巧

### 内存泄露

* LeakCanany：Square出品，集成在项目中，随时查看内存泄露的内容。
	* https://github.com/square/leakcanary 官方教程
	* http://www.liaohuqiu.net/cn/posts/leak-canary/ 中文翻译
	* https://segmentfault.com/a/1190000004925745 使用情况
	
### 权限请求以及处理（针对Android6.0以上机器适配）

* FcPermission：https://github.com/lypeer/FcPermissions 应该是目前最好用的权限请求库了
* Dexter：待探索
* PermissionHelper：待探索

### 大全套框架

* xUtils3
* Fast4Android

### 第三方服务引入

* 支付：支付宝，微信，银联，京东
* 地图：百度、高德、搜狗
* 推送：小米、jpush（极光）、云巴、个推、腾讯信鸽、DDPush（需要自己搭建服务端）
* 统计：百度统计、友盟
* 分享/登陆：ShareSDK，友盟
* 替代WebView的浏览技术： 腾讯X5浏览、mui浏览功能

	
## 代码组织架构
代码组织模式分为四类，分别为：

* MVC：最传统的代码组织模式，C层被融入到Actvity和Fragment中，导致Activity和Fragment过重，耦合程度高

* MVP：C层用Presenter来代替，从Activity和Fragment中分离出来，是Activity/Fragment更加注重于View的更新

* MVVM：View和Model进行数据绑定，形成VM层

* MVPVM：MVP+MVVM

参考文章：

* http://blog.zhaiyifan.cn/2016/03/16/android-new-project-from-0-p3/ 讲解MVPVM的
* http://blog.csdn.net/ronaldong99/article/details/50058385 MVP架构和MVC架构的对比
* http://zjutkz.net/2016/04/13/%E9%80%89%E6%8B%A9%E6%81%90%E6%83%A7%E7%97%87%E7%9A%84%E7%A6%8F%E9%9F%B3%EF%BC%81%E6%95%99%E4%BD%A0%E8%AE%A4%E6%B8%85MVC%EF%BC%8CMVP%E5%92%8CMVVM/  MVC,MVP,MVVM比较
* http://zq210wl.github.io/2015/02/03/javascript-design-pattern/  MVC,MVP,MVVM比较
* http://www.jianshu.com/p/ffcb84dc4ebc  MVC,MVP,MVVM比较

使用原则，根据项目来组织，没有最好的只有最适合的。

## 代码规范
主要考虑当前使用java编写项目，参考java代码规范即可，后续使用kotlin等新语言进行开发，需要重新考虑。

这里的Java代码规范，主要参考google的，不是一成不变，作为参考，根据这个灵活约定，尽量做到每次提交之前能够及时检查代码风格和规范！

* 参考文章：
	* https://jervyshi.gitbooks.io/google-java-styleguide-zh/content/ google的Java代码规范
	* http://www.jianshu.com/p/0a984f999592 Android编码规范
	* http://droidyue.com/blog/2016/05/22/use-checkstyle-for-better-code-style/index.html 代码检查工具


## 热修复/插件化

热修复框架列表：

* DroidFix： https://github.com/bunnyblue/DroidFix
* HotFix：也不再维护了，推荐使用RocooFix
* Nuwa：比较老了，不再维护，不推荐
* AndFix： https://github.com/alibaba/AndFix
* RocooFix：  https://github.com/dodola/RocooFix
* Small： https://github.com/wequick/Small

考虑线上项目存在bug不能及时解决的问题而提出，选择当前最合适的内容进行。

参考文章：

* http://blog.zhaiyifan.cn/2015/11/20/HotPatchCompare/ 热修复框架对比

番外：动态添加View，LuaViewSDK，聚划算团队出品，用lua语言编写，实现动态化加载原生组件！

## 持续集成
这里主要使用jenkins框架来实现，通过持续构建并打包，将繁重的打包任务放到服务端，方便测试和发版！

参考文章：

* http://debugtalk.com/post/iOS-Android-Packing-with-Jenkins/ jenkins持续集成的介绍和操作
* http://valuesfeng.github.io/2016/06/30/Android-jenkins/  jenkins持续集成的配置
* http://www.ronnywu.com/development_tool/21.html
* http://blog.csdn.net/walid1992/article/details/51493271

