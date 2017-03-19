---
title: Android数据存储（二）
date: 2015-7-12 13:03:47
categories: 读书笔记
tags: 
- Android学习
---

上一篇[Android数据存储（一）](/Android数据存储（一）/)讲的是Android的文件存储，利用的是Java的IO流对数据进行存储。这篇讲的是利用SharedPreferences对数据进行存储。
<!-- more -->

**将数据存储到SharedPreferences中**
与文件存储的方式不同，SharedPreferences是通过键值对的方式对数据进行存储的。而且SharedPreferences还支持多种不同的数据类型的存储，如果存储的数据类型是字符串，那么取出来的数据就是字符串类型的。

使用SharedPreferences来存储数据，首先需要获得到SharedPreferences对象。Android主要提供了三种方法用于得到SharedPreferences对象。

1.Context类中的getSharedPreferences()方法
此方法接收两个参数，第一个参数用于指定SharedPreferences文件的名称，如果文件不存在的话就会创建一个，如果文件已经存在则会覆盖原来的文件。SharedPreferences文件都是存放在/data/data/<package name>/shared_prefs/目录下的。第二个参数用于指定操作模式，主要有两种操作模式可以选，MODE_PRIVATE和MODE_MULTI_PROCESS。
- MODE_PRIVATE依然是默认的操作模式，和传入0效果相同，表示只有当前应用程序可以对这个SharedPreferences文件进行读写操作。
- MODE_MULTI_PROCESS用于有多个进程对同一个SharedPreferences文件进行读写操作的情况。

2.Activity类中的getPreferences()方法
这个方法与Context中的getSharedPreferences()方法很相似，不过它只接收一个操作模式参数，使用这个方法会自动将当前Activity的类名作为SharedPreferences的文件名。

3.PreferenceManager类中的getDefaultSharedPreferences()方法
这是一个静态方法，它接收一个Context参数，并自动使用当前应用程序的包名作为前缀来命名SharedPreferences文件。

**向SharedPreferences文件存储数据的步骤**
1.调用 SharedPreferences 对象的 edit()方法来获取一个 SharedPreferences.Editor 对象。
2.向 SharedPreferences.Editor 对象中添加数据，比如添加一个布尔型数据就使用
putBoolean 方法，添加一个字符串则使用 putString()方法，以此类推。
3.调用 commit()方法将添加的数据提交，从而完成数据存储操作。

**从SharedPreferences文件中读取数据**
从文件中读取数据的方法同样十分简单， SharedPreferences 对象中提供了一系列的get方法用于对存储的数据进行读取， 每种get方法都对应了SharedPreferences.Editor 中的一种 put 方法，比如读取一个布尔型数据就使用 getBoolean()方法，读取一个字符串就使用 getString()方法。这些 get 方法都接收两个参数，第一个参数是键，传入存储数据时使用的键就可以得到相应的值了，第二个参数是默认值，即表示当传入的键找不到对应的值时，会以什么样的默认值进行返回。


**Demo**
创建一个SharedPreferencesTest项目，在布局文件中添加两个按钮，一个用于存储数据，另一个用于读取数据。然后编写MainActivity的代码。
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {    
    private Button saveData;    
    private Button restoreData;    

    @Override    
    protected void onCreate(Bundle savedInstanceState) {        
        super.onCreate(savedInstanceState);        
        setContentView(R.layout.activity_main);   
     
        saveData = (Button) findViewById(R.id.save_data);        
        saveData.setOnClickListener(this);        
        restoreData = (Button) findViewById(R.id.restore_data);        
        restoreData.setOnClickListener(this);    
    }    

    @Override    
    public void onClick(View v) {      
  
        SharedPreferences preferences = getSharedPreferences("data", MODE_PRIVATE);       
        // SharedPreferences preferences = getPreferences(MODE_PRIVATE);       
        // SharedPreferences preferences = PreferenceManager.getDefaultSharedPreferences(getApplicationContext());        
        switch (v.getId()) {            
            case R.id.save_data:                
                SharedPreferences.Editor editor = preferences.edit();                
                editor.putString("name", "feiben");                
                editor.putInt("age", 22);                
                editor.putBoolean("married", false);                
                editor.commit();                
                break;            
            case R.id.restore_data:                
                String name = preferences.getString("name", "");                
                int age = preferences.getInt("age", 0);                
                boolean married = preferences.getBoolean("married", false);                
                Log.i("MainActivity", "name is " + name);                
                Log.i("MainActivity", "age is " + age);                
                Log.i("MainActivity", "married is " + married);                
            break;            
        default:                
            break;        
        }    
    }
}
```

![程序界面](http://upload-images.jianshu.io/upload_images/1917079-507cd5873e8dec40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在按下Sava data时得到SharedPreferences对象，再通过SharedPreferences.Editor将数据保存到SharedPreferences文件中。
在按下Restore data时将数据从SharedPreferences文件中取出，并打印到控制台。

![Log日志](http://upload-images.jianshu.io/upload_images/1917079-ff3c787059911605.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在Android Device Monitor中可以看到所生成的SharedPreferences文件保存在shared_prefs目录下，并且是以xml格式保存数据的。文件夹中有三个不同的文件是因为我使用了不同的方法去获取SharedPreferences对象。
![1917079-3302e91c61a994b7.png](http://upload-images.jianshu.io/upload_images/1917079-318a494abba87a0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
将data复制到电脑上，使用记事本打开可以看到里面的内容如下。可以看出于文件存储有非常明显的差别，文件存储并没有对我们的数据进行任何处理，而SharedPreferences存储数据会对我们的数据用xml格式进行格式化。
![](http://upload-images.jianshu.io/upload_images/1917079-dfcb5852df4eaeff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这篇文章就先到这里，下一篇文章[Android登陆页面记住密码以及强制下线功能的实现](/Android登陆页面记住密码以及强制下线功能的实现/)将介绍通过广播以及SharedPreferences来做一个记住账号密码功能以及强制下线功能的Demo。由于本人水平有限，如有错误，欢迎大家指正。共同学习进步！
