> 鉴于python环境开发要顾忌版本的问题，建议大家尽快迁移到3.0的环境上来，由于我的代码是2.7版本和3.0版本，环境需频繁的切换，总结了下给各位看官

# Anaconda
使用Anaconda 来快速配置python2.7和3.0的环境，快速开发，少踩坑

下载地址： https://www.anaconda.com/download/#macos 
 其实各个系统大同小异，看官们自己体会

下载完毕安装 下一步 下一步 即可
创建2个环境
名称分别为： python2 与 python3

![图.png](http://upload-images.jianshu.io/upload_images/5734876-da26515adc1232c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如何切换呢：
执行下面命令即可：
切换 python 2.7环境
```
source activate python2
```
结果如下图：
![2.7.png](http://upload-images.jianshu.io/upload_images/5734876-766a537e72f1ea44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
切换 python 3.0环境
```
source activate python3
```
结果如下图：
![3.png](http://upload-images.jianshu.io/upload_images/5734876-2c5b47353184f6b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

IDE配置需要指定当前的环境用到的是哪个：
![ide.png](http://upload-images.jianshu.io/upload_images/5734876-541f6573deb7b1c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置完毕再切换

```
source activate xxxxxx
```


