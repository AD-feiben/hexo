---
title: Android数据存储（四）
date: 2015-7-18 21:13:36
categories: 读书笔记
tags: 
- Android学习
---

这篇文章是基于[Android数据存储（三）](/Android数据存储（三）/)的基础上进行补充的，主要介绍事务的使用以及改进数据库升级的逻辑。

![](http://upload-images.jianshu.io/upload_images/1917079-393f7c91935f519f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->

####事务
事务(Transaction)是并发控制的基本单位。所谓事务,它是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位。比如你正在进行一次转账操作，银行会将转账的金额先从你的账户中扣除，然后再向收款方的账户中添加等量的金额。看上去好像没什么问题吧？可是，如果当你账户中的金额刚刚被扣除，这时由于一些异常原因导致对方收款失败，这一部分钱就凭空消失了！当然银行肯定已经充分考虑到了这种情况，它会保证扣钱和收款的操作要么一起成功，要么都不会成功，而使用的技术当然就是事务了。

接下来来看一下如何在Android中使用事务吧，打开上一篇的DatabaseTest项目，在原来的基础上进行修改。比如Book表中的数据已经很老了，现在需要全部替换成新的数据，可以先使用delete()方法将Book表中的数据先进行删除，然后再调用insert()方法将新的数据添加进Book表中。假如在我们成功删除旧数据准备添加新数据的时候，突然出现异常，那么新数据就无法添加到Book表中，Book表中什么数据都没有了，这样肯定是不行的。我们要保证的是删除旧数据和添加数据新数据要么一起完成，要么继续保留原来的旧数据。

在布局中添加一个Replace data按钮，在MainActivity中绑定Button的点击监听。
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    ……
    private MyDatabaseHelper databaseHelper;
    private ContentValues values = new ContentValues();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ……
        databaseHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
    }

    @Override
    public void onClick(View v) {    
        switch (v.getId()) {        
            ……
            case R.id.replaece_data:
                db = databaseHelper.getWritableDatabase();
                db.beginTransaction();//开启事务
                try {
                    db.delete("Book", null, null);
                    //抛出异常，让事务失败，看看旧数据是否会被删除
                    if (true) {
                        throw new NullPointerException();
                    }
                    values.clear();
                    values.put("name", "Game of Thrones");
                    values.put("author", "George Martin");
                    values.put("pages", 720);
                    values.put("price", 20.85);
                    db.insert("Book", null, values);
                    db.setTransactionSuccessful();//事务已经执行成功
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    db.endTransaction();//结束事务
                }
                break;
            default:        
                break;
        }
    }
}
```
上述代码就是Android中事务的标准用法，首先调用SQLiteDatabase的beginTransaction()方法来开启一个事务，然后在一个异常捕获的代码块中去执行具体的数据库操作，当所有的操作都完成之后，调用setTransactionSuccessful()表示事务已经执行成功了，最后在 finally代码块中调用endTransaction()来结束事务。

注意观察，我们在删除旧数据的操作完成后手动抛出了一个NullPointerException，这样添加新数据的代码就执行不到了。不过由于事务的存在，中途出现异常会导致事务的失败，此时旧数据应该是删除不掉的，接下来就来验证一下。

现在可以运行一下程序并点击Replacedata按钮，你会发现，Book表中存在的还是之前的旧数据。然后将手动抛出异常的那行代码去除，再重新运行一下程序，此时点击一下Replace data按钮就会将Book表中的数据替换成新数据了。

####改进数据库升级
上一篇我们也对数据库进行了升级，不过对升级的处理有些简单粗暴了。我们为了给数据库添加一张新的表，在onUpgrade()方法中把已经存在的表先删除了，然后强制执行一遍onCreate()方法。你要知道实际开发中这样的处理是不行的。想象以下场景，比如你编写的某个应用已经成功上线，并且还拥有了不错的下载量。现在由于添加新功能的原因，使得数据库也需要一起升级，然后用户更新了这个版本之后发现以前程序中存储的本地数据全部丢失了！那么很遗憾，你的用户群体可能已经流失一大半了。

难道产品发布出去之后还不能升级数据库了？当然不是，其实只需要进行一些合理的控制，就可以保证在升级数据库的时候数据并不会丢失了。下面就来学习一下如何实现这样的功能，每一个数据库版本都会对应一个版本号， 当指定的数据库版本号大于当前数据库版本号的时候， 就会进入到onUpgrade()方法中去执行更新操作。这里需要为每一个版本号赋予它各自改变的内容，然后在onUpgrade()方法中对当前数据库的版本号进行判断，再执行相应的改变就可以了。

下面就来看看如何实现合理升级数据库的操作，打开MyDatabaseHelper类进行修改。假如说第一个版本的数据库只有一张Book表，那么这样写就可以实现了
```java
public class MyDatabaseHelper extends SQLiteOpenHelper {

    private static final String CREATE_BOOK = "create table Book(" +
            "id integer primary key autoincrement," +
            "name text," +
            "author text," +
            "pages integer," +
            "price real)" ;

    public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
        this.context = context;
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_BOOK);
        Toast.makeText(context, "Database Created succeeded", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```
以上代码就可以实现Book表的创建了，假如在第2个版本的数据库中需要加入Category表，那么只需要将代码这样修改即可。
```java
public class MyDatabaseHelper extends SQLiteOpenHelper {

    ……
    private static final String CREATE_CATEGORY = "create table Category(" +
        "id integer primary key autoincrement," +
        "category_name text," +
        "category_cade integer)";**
    ……

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_BOOK);
        db.execSQL(CREATE_CATEGORY);
        Toast.makeText(context, "Database Created succeeded", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        switch (oldVersion) {
            case 1:
                db.execSQL(CREATE_CATEGORY);
            default:
        }
    }
}
```
这样就可以实现Category表的添加而不影响Book表的数据了，为什么在onCreate()方法和onUpgrade()方法都完成Category表的创建。原因很简单，假如你是在APP中的数据库升级为第2个版本之后才注册的新用户，那么你一开始创建的数据库就是第2个版本的数据库，那么你在创建数据库的时候就会调用onCreate()方法，这样就会同时创建Bookb表和Category表；但是那些一开始就是用这个APP的用户就不会再执行onCreate()方法了，因为数据库已经存在了，他们只会从第1个版本升级为第2个版本，所以在onUpgrade()中也需要实现创建Category表，这样才能老用户在不丢失数据的情况下升级到与新用户的相同的数据库。

**需要注意的是每个case语句最后都不加上break，这样不管你是从哪个版本升级到最新的数据库，都不会缺失其中每一次的升级。比如说你从第1个版本升级到第4个版本，那么你不仅仅执行case1就可以了，你还要执行case2和case3才会升级到和目标相同的数据库。**

>如果文章对你有所帮助，那么请您点一下❤
>由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
