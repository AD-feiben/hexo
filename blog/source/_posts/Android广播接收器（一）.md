---
title: Android广播接收器（一）
date: 2015-7-1 11:42:37
categories: 读书笔记
tags: 
- Android学习
---

###广播的种类
**1.标准广播**
标准广播是一种完全异步执行的广播，在广播发出之后，所有的广播接收器几乎会同时接收到这条广播消息。此类广播效率较高而且不能截断。![](http://upload-images.jianshu.io/upload_images/1917079-00a6b9ddd0a874a8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->
**2.有序广播**
有序广播是一种同步执行的广播，广播发出之后，优先级高的广播接收器就可以先接收到广播消息，执行完该广播接收器的逻辑后，可以选择截断正在传递的广播或者继续传递，如果广播消息被截断，之后的广播接收器则无法收到广播消息。![](http://upload-images.jianshu.io/upload_images/1917079-31f3a8f2a27d905f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###接收系统广播
注册广播的方式一般有两种，在代码中注册称为动态注册；在AndroidManifest.xml中注册称为静态注册。
####1.动态注册广播接收器
下面是通过一个监听网络变化的demo来学习广播接收器的动态注册。
首先需要新建一个类，继承BroadcastReceiver并复写父类的onReceive()方法就行了。当有广播到来时，onReceiver()方法就会得到执行。
```java
public class MainActivity extends AppCompatActivity { 

  private IntentFilter intentFilter; 
  private NetworkChangeReceiver networkChangeReceiver; 

  @Override 
  protected void onCreate(Bundle savedInstanceState) { 
    super.onCreate(savedInstanceState); 
    setContentView(R.layout.activity_main); 

    intentFilter = new IntentFilter(); 
    intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE"); 
    networkChangeReceiver = new NetworkChangeReceiver(); 
    registerReceiver(networkChangeReceiver, intentFilter); 
  } 

  @Override 
  protected void onDestroy() { 
    super.onDestroy(); 
    unregisterReceiver(networkChangeReceiver); 
  } 

  class NetworkChangeReceiver extends BroadcastReceiver {

    @Override 
    public void onReceive(Context context, Intent intent) {

      ConnectivityManager connectivityManager = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE); 
      NetworkInfo networkInfo = connectivityManager.getActiveNetworkInfo(); 

      if (networkInfo != null && networkInfo.isAvailable()) { 
        Toast.makeText(context, "network is available", Toast.LENGTH_SHORT).show(); 
      } else { 
        Toast.makeText(context, "network is unavailbale", Toast.LENGTH_SHORT).show(); 
      } 
    }
  }

}
```
在MainActivity中定义了一个内部类NetworkChangeReceiver继承自BroadcastReceiver，并复写父类的onReceive()方法。每当网络状态发生变化时，onReceive()方法就会得到执行。在onReceive()方法中，首先通过getSystemService()方法得到ConnectivityManager的实例，这是一个系统服务类，专门用于管理网络连接的。再调用它的getActiveNetworkInfo()方法得到NetworkInfo的实例，接着调用NetworkInfo的isAvailable()方法就可以判断出当前是否有网络了。

在onCreate()方法中，首先创建了一个IntentFilter的实例，并添加一个值为android.net.conn.CONNECTIVITY_CHANGE的action。
>想要监听什么广播，只需要在这里添加相应的action

接下来创建一个NetworkChangeReceiver实例，最后调用registerReceiver()方法进行注册，将NetworkChangeReceiver和IntentFilter作为参数进去，这样就可以实现了监听网络变化的功能。这种方法就称为动态注册广播接收器。

**需要注意的是:**
动态注册广播接收器一定都要取消注册。在onDestroy()方法中调用unregisterReceiver()方法就可以实现取消注册了。

**最后不要忘了读取网络状态需要添加读取网络状态的权限**
```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```
####2.静态注册广播接收器
动态注册的广播接收器可以自由地控制注册于注销，在灵活性方面有很大的优势，但是它必须要在程序启动之后才能接收到广播，因为注册的代码是写在onCreate()方法中的。如果需要在程序未启动的情况下就能接收到广播就需要用到静态注册的方法了。下面是通过静态注册广播接收器的方法来实现对开机广播的接收。
- 首先新建一个BootCompleteReceiver类继承BroadcastReceiver

```java
public class BootCompleteReceiver extends BroadcastReceiver {
 
  @Override 
  public void onReceive(Context context, Intent intent) {
     Toast.makeText(context,"Boot Complete",Toast.LENGTH_LONG).show();  
  }
}
```
- 接下来需要在AndroidManifest.xml声明BootCompleteReceiver这个类，BroadcastReceiver是安卓的四大组件之一，所以以下代码需要写在<application>标签内
```xml
<receiver android:name=".BootCompleteReceiver">
    <intent-filter> 
      <action android:name="android.intent.action.BOOT_COMPLETED"/>   
    </intent-filter>
</receiver>
```
- 监听系统开机广播同样需要声明权限
```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
这样就可以实现广播的静态注册了。

这篇文章就先到这里，下一篇文章[Android广播接收器（二）](/Android广播接收器（二）/)将介绍如何来发送自定义广播。由于本人水平有限，如有错误，欢迎大家指正。共同学习进步！
