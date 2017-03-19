---
title: Android广播接收器（三）
date: 2015-7-5 20:59:31
categories: 读书笔记
tags: 
- Android学习
---

##本地广播

前面两篇文章[Android广播接收器（一）](/Android广播接收器（一）/)和[Android广播接收器（二）](/Android广播接收器（二）/)讲的都属于全局广播，即发出的广播可以被其他任何应用程序接收到，同样我们也可以接受来自其他应用程序的广播，这样就容易引起安全问题。

利用本地广播就可以解决广播的安全问题，这类广播只能在应用程序的内部进行传递，并且广播接收器也只能接收来自本应用程序发出的广播。
<!-- more -->

本地广播主要就是使用一个LocalBroadcastManager来管理广播，提供了发送广播和注册广播接收器的方法。接下来通过代码看看如何实现本地广播,主要还是通过之前建好的BroadcastTest这个项目来进行测试，修改MainActivity的代码即可。
```java
public class MainActivity extends AppCompatActivity {
    
    private IntentFilter intentFilter;    
    private LocalBroadcastManager localBroadcastManager;    
    private LocalReceiver localReceiver;    

    @Override    
    protected void onCreate(Bundle savedInstanceState) {        
    
        super.onCreate(savedInstanceState);        
        setContentView(R.layout.activity_main); 
       
        localBroadcastManager = localBroadcastManager.getInstance(this);//获取实例        
        intentFilter = new IntentFilter();        
        intentFilter.addAction("com.feiben.broadcasttest.LOCAL_BROADCAST");   
        localReceiver = new LocalReceiver();        
        localBroadcastManager.registerReceiver(localReceiver, intentFilter);//注册本地广播监听器
        
        Button btnSendBroadcast = (Button) findViewById(R.id.btn_send_broadcast);        
        btnSendBroadcast.setOnClickListener(new View.OnClickListener() {            
            @Override            
            public void onClick(View v) {                
                Intent intent = new Intent("com.feiben.broadcasttest.LOCAL_BROADCAST");                
                localBroadcastManager.sendBroadcast(intent);//发送本地广播            
            }        
        });    
    }    

    @Override    
    protected void onDestroy() {        
        super.onDestroy();        
        localBroadcastManager.unregisterReceiver(localReceiver);    
    } 
   
    class LocalReceiver extends BroadcastReceiver {        

        @Override       
        public void onReceive(Context context, Intent intent) {            
            Toast.makeText(context,"received local broadcast",Toast.LENGTH_SHORT).show();        
        }    
    }
}
```
可以看出本地广播与之前的动态注册广播接收器的方法基本上是一致的。不同的只是在先通过LocalBroadcastManager的getInstance()方法得到一个LocalBroadcastManager的实例，然后在注册以及发送广播的时候分别调用LocalBroadcastManager的registerReceiver()方法和sendBroadcast()方法。

为了验证本地广播只能在BroadcastTest这个程序内传播，我在BroadcastTest2项目中也来接收com.feiben.broadcasttest.LOCAL_BROADCAST这条广播。打开BroadcastTest2修改MainActivity的代码,将原来的静态注册广播改为动态注册。
```java
public class MainActivity extends AppCompatActivity {    

    private IntentFilter intentFilter;    
    private AnotherBroadcastReceiver receiver;    

    @Override    
    protected void onCreate(Bundle savedInstanceState) {        
        super.onCreate(savedInstanceState);        
        setContentView(R.layout.activity_main);   
          
        intentFilter = new IntentFilter();        
        intentFilter.addAction("com.feiben.broadcasttest.LOCAL_BROADCAST");        
        receiver = new AnotherBroadcastReceiver();        
        registerReceiver(receiver,intentFilter);    
    }
}
```
将BroadcastTest2安装到模拟器上，再启动BroadcastTest，点击Send Braodcast这个按钮，可以看到Toast只显示了一次，BraodcastTest2接收不到com.feiben.broadcasttest.LOCAL_BROADCAST这条广播。
>本地广播只能通过动态注册的方式来接收。

![](http://upload-images.jianshu.io/upload_images/1917079-3cf9db2f162a8be3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
总结一下本地广播的几点优势：
- 本地广播发送的广播只能在我们的程序内部传播，所以不用担心机密数据泄漏的问题。
- 其他的程序也无法将广播发送到我们程序的内部，因此不用担心会有安全漏洞的隐患。
- 发送本地广播比起发送全局广播更加高效。

Android广播接收器的内容就到这里，下一篇文章[Android数据存储（一）](/Android广播接收器（二）/)将开始介绍Android的数据存储。由于本人水平有限，如有错误，欢迎大家指正。共同学习进步！
