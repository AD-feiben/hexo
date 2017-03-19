---
title: Android登录页面记住密码以及强制下线功能的实现
date: 2015-7-13 22:43:06
categories: 读书笔记
tags: 
- Android学习
---

这篇文章主要是巩固一下前面所学的SharedPreferences存储数据以及广播接收器。如果对这两个部分不熟悉的话可以先看一下[Android数据存储（二）](/Android数据存储（二）/)以及[Android广播接收器（二）](/Android广播接收器（二）/)由于这篇文章的代码就多，所以就在文章里只展示了比较重要的部分代码。完整代码在[github地址](https://github.com/AD-feiben/RememberPasswordTest)。程序运行效果如下图
![](http://upload-images.jianshu.io/upload_images/1917079-6c5cb8560d65a191.gif?imageMogr2/auto-orient/strip)
<!-- more -->

**Activity的管理**
为了更好的管理Activity，先创建一个ActivityCollector类用于管理所有的Activity。代码如下
```java
public class ActivityCollector {
    public static List<Activity> activities = new ArrayList<Activity>();

    public static void addActivity(Activity activity) {
        activities.add(activity);
    }

    public static void removeActivity(Activity activity){
        activities.remove(activity);
    }

    public static void finishAll(){
        for(Activity activity:activities){
            if(!activity.isFinishing()){
                activity.finish();
            }
        }
    }
}
```
下面再创建一个BaseActivity类来作为每个Activity的父类，代码如下
```java
public class BaseActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityCollector.addActivity(this);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        ActivityCollector.removeActivity(this);
    }
}
```
可以看到BaseActivity也是非常简单，继承自Activity复写onCreate()并调用ActivityCollector.addActivity()方法以及复写onDestroy()并调用ActivityCollector.removeActivity()方法。在创建每个Activity时继承BaseActivity就可以方便的管理每个Activity了。这样做的好处就是不论在哪个Activity中接收强制下线的广播时都可以轻松地finish所有的Activity，而且广播接收器也不用依附于任意一个Activity。

**登陆界面**
```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_height="wrap_content"
            android:layout_marginLeft="10dp"
            android:layout_marginTop="10dp"
            android:text="Account:"
            android:textSize="18sp" />

        <EditText
            android:id="@+id/account_edit_text"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:hint="Input your account " />
    </TableRow>
    <TableRow> ……/>
    <TableRow>
        <CheckBox
            android:id="@+id/remember_password"
            android:layout_marginLeft="10dp" />

        <TextView
            android:layout_height="wrap_content"
            android:text="Remember password"
            android:textSize="18sp" />
    </TableRow>
    <TableRow>
        <Button
            android:id="@+id/login"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:layout_span="2"
            android:text="Login"
            android:textAllCaps="false" />
    </TableRow>
</TableLayout>
```
布局界面主要通过TableLayout来实现的，第二个<TableRow>与第一个类似都是一个TextView和一个EditText，所以第二个就省略了。显示效果如下图所示

![登陆界面布局](http://upload-images.jianshu.io/upload_images/1917079-38d3b9c129aa02b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**记住密码功能的实现，代码如下**
```java
public class LoginActivity extends BaseActivity {

    private SharedPreferences preferences;
    private SharedPreferences.Editor editor;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        preferences = PreferenceManager.getDefaultSharedPreferences(this);
        isRemember = preferences.getBoolean("rememberPassword", false);

        login.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String account = accountEditText.getText().toString();
                String password = passwordEditText.getText().toString();

                if (account.equals("admin") && password.equals("123456")) {
                    editor = preferences.edit();
                    if (rememberPassword.isChecked()) {
                        editor.putString("account", account);
                        editor.putString("password", password);
                        editor.putBoolean("rememberPassword", true);
                    } else {
                        editor.clear();
                    }
                    editor.commit();
                    Intent intent = new Intent(LoginActivity.this, MainActivity.class);
                    startActivity(intent);
                    finish();
                } else if (account.equals("") && password.equals("")) {
                    Toast.makeText(LoginActivity.this,"account or password cannot be empty",Toast.LENGTH_SHORT).show();
                } else {
                    Toast.makeText(LoginActivity.this, "account or password is invalid", Toast.LENGTH_SHORT).show();
                }
            }
        });

        if (isRemember) {
            accountEditText.setText(preferences.getString("account", ""));
            passwordEditText.setText(preferences.getString("password", ""));
            rememberPassword.setChecked(isRemember);
        }
    }
}
```
程序一开始就实例化一个SharedPreferences对象，并从SharedPreferences文件中读取键为"rememberPassword"的值并赋值给isRemember这个变量来确定是否有记住密码。如果没读取到这个值，默认就为false即没有记住密码；如果有值并且为true的话就会执行if(isRemember)这个方法，从SharedPreferences文件中读取之前保存的账户和密码并且将数据显示到对应的EditText中。

接下来看的是login按钮的监听，首先判断账户密码是否正确，如果正确的话再判断是否勾选了记住密码。如果选中了记住密码，就会通过SharedPreferences.Editor将EditText的内容以及CheckBox的状态保存到SharedPreferences文件中；如果没有选中记住密码，就会将SharedPreferences.Editor清空。然后再通过Intent进入MainActivity。

**强制下线功能的实现**
在主界面中，我放置了一个Button用于发送强制下线的广播，布局就不写出来了。主要还是看如何实现强制下线。
```java
public class MainActivity extends BaseActivity {

    private Button forceOffline;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.layout_main);
        forceOffline = (Button) findViewById(R.id.force_offline);

        forceOffline.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent("com.feiben.rememberpasswordtest.FORCE_OFFLINE");
                sendBroadcast(intent);
            }
        });
    }
}
```
MainActivity也是十分简单，主要就是发送一个action为"com.feiben.rememberpasswordtest.FORCE_OFFLINE"的广播。想必你应该也猜到这里使用的是静态注册的方法了吧。所以需要在创建一个ForceOfflineReceiver类用来接收广播。

```java
public class ForceOfflineReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(final Context context, Intent intent) {
        AlertDialog.Builder builder = new AlertDialog.Builder(context);
        builder.setTitle("Warning");
        builder.setMessage("You are forced to be offline.Please try to login again");
        builder.setCancelable(false);
        builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                ActivityCollector.finishAll();
                Intent intent = new Intent(context,LoginActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                context.startActivity(intent);
            }
        });

        AlertDialog alertDialog = builder.create();
        alertDialog.getWindow().setType(WindowManager.LayoutParams.TYPE_SYSTEM_ALERT);
        alertDialog.show();
    }
}
```
每当接收到广播时就会弹出一个Dialog通知用户被强制下线了，需要重新登陆。首先实例化一个AlertDialog.Builder对象，并设置其Title、Message以及setPositiveButton。这里的setCancelable(false)主要是让用户只能点击PositiveButton来取消弹窗，如果没有设置这个属性的话，用户只需要按下返回按钮就可以将Dialog取消掉并继续使用程序，很显然这并不符合正常的逻辑。

接下来在给PositiveButton设置监听器，一旦点击PositiveButton就会调用ActivityCollector.finishAll()方法将所有的Activity给Finish掉，再通过Intent来启动LoginActivity来让用户重新登陆。因为实在广播接收里启动Activity，所以需要加上intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)，具体用法可以看[Android Intent Flag的介绍](http://www.jianshu.com/p/a625c92f18d5)。最后需要将Dialog的类型设置为WindowManager.LayoutParams.TYPE_SYSTEM_ALERT才能保证弹窗的正常显示。

最后再看一眼manifest.xml中需要注意的地方
```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>


<activity android:name=".LoginActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

<activity android:name=".MainActivity" />

<receiver android:name=".ForceOfflineReceiver">
    <intent-filter>
        <action android:name="com.feiben.rememberpasswordtest.FORCE_OFFLINE"/>
    </intent-filter>
</receiver>
```
1.因为Dialog的类型设置为WindowManager.LayoutParams.TYPE_SYSTEM_ALERT所以需要加上android.permission.SYSTEM_ALERT_WINDOW这个权限。

2.将LoginActivity设置为主Activity这样程序一启动就会开启LoginActivity。

3.因为广播接收器使用的是静态注册的方法，所以在manifest进行注册。

MainActivity以及弹窗的效果如下图

![](http://upload-images.jianshu.io/upload_images/1917079-f0c9361abf596263.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

