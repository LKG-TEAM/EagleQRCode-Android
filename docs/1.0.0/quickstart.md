# EagleGesturePassword
模块介绍
# ScreenShot

# Add EagleGesturePassword to your project
在project根目录的build.gradle里
```
    repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
        maven { url "http://192.168.2.94:10080/repertory/maven/dependency/raw/master/" }
    }
```
在module的build.gradle里
```
compile 'com.linkstec.common.eaglegesturepassword:EagleGesturePassword:1.0.2
```
其中1.0.2换成最新的版本，或者使用1.+的形式。
# About this project

# ProGuard
不需要配置

# How do I use EagleGesturePassword
### **1. 使用模块**

根据路由地址,直接启动模块
```
 navigateTo("");
```
根据路由地址和参数,启动模块
```
 navigateTo("", bundle);
```

# Samples
开发者自实现

# Author
E-MAIN:


# git











