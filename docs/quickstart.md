# EagleQRCode

`EagleQRCode` 是凌志软件移动(Eagle)应用app模块，负责二维码和条形码扫描和生成

# ScreenShot

# Add EagleQRCode to your project
在project根目录的build.gradle里
```groovy
    repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
        maven { url "http://192.168.2.94:10080/repertory/maven/dependency/raw/master/" }
    }
```
在module的build.gradle里
```groovy
compile 'com.linkstec.tools.eagleqrcode:EagleQRCode:1.0.1'
```
其中1.0.1换成最新的版本，或者使用1.+的形式。

# About this project

# ProGuard
不需要配置

# How do I use EagleQRCode
### **1. 使用模块**

根据路由地址,直接启动模块
```groovy
 navigateTo("/eaglemedia/qrscanner");
```
根据路由地址和参数,启动模块
```groovy
 navigateTo("/eaglemedia/qrscanner", bundle);
```

# Samples
开发者自实现

# Author
E-MAIN:


# git











