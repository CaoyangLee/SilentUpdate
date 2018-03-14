# 静默更新应用库
[![License](https://img.shields.io/badge/license-Apache%202-green.svg)](https://www.apache.org/licenses/LICENSE-2.0)
[ ![Download](https://api.bintray.com/packages/yongdongji/android/silentupdate/images/download.svg) ](https://bintray.com/yongdongji/android/silentupdate/_latestVersion)

> 注意：此库是由kotlin编写而成<br>


## 演示
![](https://github.com/CaoyangLee/SilentUpdate/blob/master/gif/gif_demo.gif)

A library silently & automatically download latest apk to update your App<br>
静默自动下载最新apk并升级应用

# 双策略执行步骤
1. 判断权限【使用者】
2. 获取下载链接，判断版本号【使用者自己实现】
3. 开始下载前，判断升级文件是否存在，**存在**：显示安装文件Dialog和回调(onFileIsExist) 

一：Wifi的情况【静默】<br>

4. 下载时,是静默状态，不会有通知栏显示进度
5. 下载完成,接收回调(onDownLoadSuccess),显示Notification和Dialog
6. 用户点击Dialog或Notification即跳转到安装界面

二：流量的情况<br>

4. 显示提示用户下载更新app的Dialog，用户点击更新后，开始下载操作，
5. 下载时,通知栏会显示下载进度
5. 下载完成后，接收回调(onDownLoadSuccess)并跳转安装界面


> 注意：以下为Kotlin的操作，若使用Java请点击[这里](https://github.com/CaoyangLee/SilentUpdateDemo/blob/master/README_JAVA.md)

## 准备工作 
1.获取依赖

**project的build.gradle**

```
allprojects {
    repositories {
        ......        
        maven { url  "https://dl.bintray.com/yongdongji/android" }
    }
}
```
**app的build.gradle**
[ ![Download](https://api.bintray.com/packages/yongdongji/android/silentupdate/images/download.svg) ](https://bintray.com/yongdongji/android/silentupdate/_latestVersion)

> 注意：默认使用kotlin1.2.20版本的库

```gradle
compile "org.jetbrains.kotlin:kotlin-stdlib-jdk7:1.2.20"
compile 'www.weimu.io:silentupdate:{version_code}@aar'
```

2.增加权限

```xml
<!-- 联网权限 -->
<uses-permission android:name="android.permission.INTERNET" />
<!-- 存储权限 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<!-- 通知权限 -->
<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
```       
3.增加FileProvider【适配7.0】

> 注意：此处的```android:resource="@xml/filepaths"```自己谷歌或直接获取[demo文件](https://github.com/CaoyangLee/SilentUpdate/blob/master/app/src/main/res/xml/filepaths.xml)

```xml
<provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="${applicationId}.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/filepaths" />
</provider>
```


4.在Application中进行初始化

```kotlin
UpdateCenter.init(this)
```

## 用法
> 注意：<br>
apkUrl：服务器提供的apk下载地址<br>
latestVersion：服务器返回客户端的最新版本号

```kotlin
UpdateCenter.update(apkUrl, latestVersion)
```

## 自定义配置
1.开关显示自带Notification和dialog<br>

```kotlin
UpdateCenter.isUseDefaultHint = false//是否使用默认提示 包括Dialog和Notification
```

2.实现回调<br>
> 注意：默认情况下，【下载完成】或【文件已存在】都会有默认的Notification和Dialog提示。<br>
若想自定义提示，请实现以下接口并配合【自定义配置】的第一步

* 执行下载任务之前都会判断更新文件是否存在，**存在**：调用`onFileIsExist(file: File)`，不再进行下载操作
* 下载完成则调用`onDownLoadSuccess(file: File)`

```kotlin
UpdateCenter.downloadListener = object : DownloadListener {

    override fun onDownLoadSuccess(file: File) {
        //下载完成
    }

    override fun onFileIsExist(file: File) {
        //文件已存在
    }

}
```
