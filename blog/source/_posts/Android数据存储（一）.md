---
title: Android数据存储（一）
date: 2015-7-10 9:39:07
categories: 读书笔记
tags: 
- Android学习
---

![](http://upload-images.jianshu.io/upload_images/1917079-6c19aa249b3d5e7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

任何应用与用户直接交互的除了UI就是数据了，一个没有数据的应用程序只是一个空壳子，毫无作用。之前写过很多的例子中也用到了各种各样的数据，但这些数据都属于瞬时数据。瞬时数据指的就是那些存储在内存当中，有可能会因为程序关闭或者其他原因导致内存被回收而丢失的数据。对于一些关键性的数据就需要保证不会因某种原因而丢失，这就需要用到数据持久化技术了。
<!-- more -->

**数据持久化技术简介**
数据持久化就是指将那些内存中的瞬时数据保存在存储设备中，保证即使在手机或电脑关机的情况下，这些数据依然不会丢失。保存在内存中的数据是处于瞬时状态，而保存在存储设备中的数据是处于持久状态的，持久化技术则是提供了一种机制可以让数据在瞬时状态和持久状态之间进行转换。

**Android系统的数据持久化方式**
Android系统主要提供了四种方式，其中包括文件存储、SharedPreference存储、数据库存储以及ContentProvider。下面开始介绍各种数据持久化方式的用法。
>ContentProvider作为四大组件之一，这里暂时就先不讲

**文件存储**
文件存储是Android中最基本的一种数据存储方式，它不对存储的内容进行任何的格式化处理，所有数据都是原封不动地保存到文件中，因而它比较适合用于存储一些简单的文本数据或者二进制数据。

- 将数据存储到文件中

```java
public void save(String data) {
    FileOutputStream out = null;
    BufferedWriter writer = null;
    try {
        out = openFileOutput("data",Context.MODE_PRIVATE);
        writer = new BufferedWriter(new OutputStreamWriter(out));
        writer.write(data);
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if(writer != null) {
                writer.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
Context类中提供了一个openFileOutput()方法，可以用于将数据存储到指定的文件中。该方法接收两个参数，第一个参数为文件名，文件创建的时候使用的就是这个名称。（注：文件名不能包含路径，因为所有文件都默认存储到/data/data/<package name>/files/目录下的）。第二个参数是文件的操作模式，主要有两种模式可选，MODE_PRIVATE和MODE_APPEND。其中MODE_PRIVATE为默认的操作模式，表示当指定同样文件名的时候，所写入的内容将会覆盖源文件中的内容；而MODE_APPEND则表示如果该文件已存在就往文件里面追加内容，不存在则创建新文件。

openFileOutput()方法返回一个FileOutputStream对象，得到这个对象之后就可以使用Java的IO流操作将数据写入文件中了。

- 从文件中读取数据

和数据存储到文件中十分类似，Context还提供了一个openFileInput()方法，用于从文件中读取数据。这个方法比openFileOutput()还要简单些，它只接收一个参数，既要读取的文件名，然后系统会自动到/data/data/<package name>/files/目录下去加载这个文件，并返回一个FileInputStream对象，再通过Java IO流的方式就可以将数据读取出来了。
```java
public String load() {
    FileInputStream in = null;
    BufferedReader reader = null;
    StringBuilder content = new StringBuilder();
    try {
        in = openFileInput("data");
        reader = new BufferedReader(new InputStreamReader(in));
        String line = " ";
        while((line = reader.readLine()) != null){
            content.append(line);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            } 
        }
    }
    return content.toString();
}
```
---
**Demo**
```java
public class MainActivity extends AppCompatActivity {    
    private EditText editText;    
    private String data;  
  
    @Override    
    protected void onCreate(Bundle savedInstanceState) {       
 
        super.onCreate(savedInstanceState);        
        setContentView(R.layout.activity_main);        
        editText = (EditText) findViewById(R.id.edit_text);        
        data = load();        

        if (!TextUtils.isEmpty(data)) {            
            editText.setText(data);            
            editText.setSelection(data.length());            
            Toast.makeText(this, "Restoring succeeded", Toast.LENGTH_SHORT).show();        
        }    
    }    

    @Override    
    protected void onDestroy() {        
        super.onDestroy();        
        data = editText.getText().toString();        
        save(data);    
    }    

    private void save(String data) {……}
    private String load() {……}
}
```
>save()方法和load()方法与前面一样就不再重写了，布局只有一个EditText所以布局的代码也不写出来了。

第一次启动程序后在EditText中输入"Hello"后，按返回键退出程序再重新启动程序时效果如下图所示，说明数据保存成功并且也成功地从文件中读取到数据。
![](http://upload-images.jianshu.io/upload_images/1917079-f912fea4dc35423b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们也可以利用Android Studio自带的Android Devices Monitor来查看刚刚保存数据的文件，打开Android Devices Monitor选择右侧的File Explorer选项卡进入/data/data/<package name>/files/目录下就可以看到刚刚保存数据的文件了。

![](http://upload-images.jianshu.io/upload_images/1917079-5654c21dc3cd31c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击右上角的Pull a file from the device按钮就可以将文件复制到我们的电脑上了，用记事本将data文件打开就可以看到里面所保存的数据了。


![](http://upload-images.jianshu.io/upload_images/1917079-548f2d282f1b549d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出使用文件存储并不会对我们的数据进行任何处理。

这篇文章就先到这里，下一篇文章[Android数据存储（二）](/Android数据存储（二）/)将介绍使用SharedPreferences来存储数据。由于本人水平有限，如有错误，欢迎大家指正。共同学习进步！
