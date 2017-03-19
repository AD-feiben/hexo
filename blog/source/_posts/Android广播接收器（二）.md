---
title: Android广播接收器（二）
date: 2015-7-2 15:28:57
categories: 读书笔记
tags: 
- Android学习
---

上一篇[Android广播接收器（一）](/Android广播接收器（一）/)讲了使用动态注册和静态静注册广播接收器的方法来接收系统发出的广播。这篇要讲的是如何来发送自定义广播。
<!-- more -->
####1.发送标准广播
- 首先新建一个类继承自BroadcastReceiver，用来接收广播
```java
public class MyBroadcastReceiver extends BroadcastReceiver { 

    @Override 
    public void onReceive(Context context, Intent intent) { 
      Toast.makeText(context,"recived in MyBroadcastReceiver",Toast.LENGTH_SHORT).show(); 
    }
}
```
- 接下来需要在AndroidManifest.xml对这个广播接收器进行注册
``` xml
<receiver android:name=".MyBroadcastReceiver"> 
    <intent-filter> 
      <action android:name="com.feiben.broadcasttest.MY_BROADCAST"/> 
    </intent-filter>
</receiver>
```
>类似于上一篇讲的静态注册广播接收器的方法,但这里的action是我们自己定义的。
- 在布局文件中放置一个Button，用来作为发送广播的触发点
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
android:layout_width="match_parent" 
android:layout_height="match_parent"> 

    <Button 
      android:id="@+id/btn_send_broadcast" 
      android:layout_width="match_parent" 
      android:layout_height="wrap_content" 
      android:text="Send Broadcast" 
      android:textAllCaps="false" />
</LinearLayout>
```
- 在MainActivity中实现Button的逻辑处理
```java
public class MainActivity extends AppCompatActivity { 

    @Override 
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState); 
        setContentView(R.layout.activity_main)；

        Button btnSendBroadcast = (Button) findViewById(R.id.btn_send_broadcast); 
        btnSendBroadcast.setOnClickListener(new View.OnClickListener() { 

            @Override 
            public void onClick(View v) { 
              Intent intent = new Intent("com.feiben.broadcasttest.MY_BROADCAST"); 
              sendBroadcast(intent); 
            } 
        }); 
    }
}
```
每次点击Button就会创建一个Intent实例，并把要发送的广播的值传进去，在调用Context的sendBroadcast()方法将广播发送出去,这样发出的就是一条标准广播了。效果如下

![](http://upload-images.jianshu.io/upload_images/1917079-86d6b79dc49bc602.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
####2.发送有序广播
广播是一种跨进程的通信方式，因此在我们的应用程序内发出的广播，其他的应程序也是可以收到的。因此在这新建一个BroadcastTest2的项目来验证这一点。
- 新建项目之后新建一个AnotherBroadcastReceiver类继承BroadcastReceiver来接收上一节的广播。
```java
public class AnotherBroadcastReceiver extends BroadcastReceiver {
    @Override 
    public void onReceive(Context context, Intent intent) { 
      Toast.makeText(context,"received in AnotherBroadcastReceiver",Toast.LENGTH_SHORT).show(); 
    }
}
```
- 接着在AndroidManifest.xml中注册广播接收器
```xml
<receiver android:name=".AnotherBroadcastReceiver"> 
    <intent-filter> 
      <action android:name="com.feiben.broadcasttest.MY_BROADCAST"/> 
    </intent-filter>
</receiver>
```
可以看到AnotherBrcom.feiben.broadcastReceiver同样接收到com.feiben.brcom.feiben.broadcasttest.MY_BROADCAST这条广播
![](http://upload-images.jianshu.io/upload_images/1917079-2037a9955145507f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/1917079-e25b9502723857ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
到现在为止程序发出的还是标准广播，接下来就来看看怎么发出有序广播。先关闭BroadcastTest2这个项目，然后修改BroadcastTest项目MainActivity的代码。
```java
public class MainActivity extends AppCompatActivity { 

    @Override 
    protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState); 
      setContentView(R.layout.activity_main)；

      Button btnSendBroadcast = (Button) findViewById(R.id.btn_send_broadcast); 
      btnSendBroadcast.setOnClickListener(new View.OnClickListener() { 

        @Override 
        public void onClick(View v) { 
          Intent intent = new Intent("com.feiben.broadcasttest.MY_BROADCAST"); 
          sendOrderedBroadcast(intent，null); 
        } 
      });
    }
 }
```
发送有序广播只需要修改一行代码即可，即将sendBroadcast()方法改为sendOrderedBroadcast()方法，第一个参数仍是Intent，第二个参数是一个权限相关的字符串，传入null就行。重新运行程序，可以发现与前面的标准广播并无区别。但其实有序广播发送是有先后顺序的，而且优先级高的在接收到广播之后还可以对其进行截断，阻止继续广播。怎么设定广播接收器的优先级呢？这就需要在AndroidManifest.xml中进行设定了。
```xml
<receiver android:name=".MyBroadcastReceiver"> 
    <intent-filter android:priority="100"> 
      <action android:name="com.feiben.broadcasttest.MY_BROADCAST"/> 
    </intent-filter>
</receiver>
```
可以看到，利用android:priority属性就可以给广播接收器设置优先级了。这里把优先级设置为100，保证它一定会在AnotherBroadcastReceiver之前收到广播了。接着就来试一下阻止这条广播的继续传递。修改MyBroadcastReceiver中的代码。
```java
public class MyBroadcastReceiver extends BroadcastReceiver { 

    @Override 
    public void onReceive(Context context, Intent intent) { 

      Toast.makeText(context,"recived in MyBroadcastReceiver",Toast.LENGTH_SHORT).show(); 
      abortBroadcast();
    }
}
```
在onReceive()方法中调用abortBroadcast()方法就可以将广播截断了。

这篇文章就先到这里，下一篇文章[Android广播接收器（三）](/Android广播接收器（三）/)将介绍本地广播。由于本人水平有限，如有错误，欢迎大家指正。共同学习进步！
