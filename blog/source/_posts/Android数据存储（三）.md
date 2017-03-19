---
title: Android数据存储（三）
date: 2015-7-15 19:29:27
categories: 读书笔记
tags: 
- Android学习
---

前面两篇文章[Android数据存储（一）](/Android数据存储（一）/)和[Android数据存储（二）](/Android数据存储（二）/)分别使用文件存储、SharedPreferences的方式来对数据进行存储。这篇主要是讲SQLite数据库的创建，表的创建，以及数据的增删改查。为了保持连贯性，我就把全部内容都在这篇文章中讲了，所以内容稍微有点长。文中使用ADB工具来查看数据库中的数据，如果没有安装ADB调试工具的话，请自行安装。完整代码在：[https://github.com/AD-feiben/DatabaseTest](https://github.com/AD-feiben/DatabaseTest)
![](http://upload-images.jianshu.io/upload_images/1917079-3e6ef60b06bb764e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->

###为什么要使用SQLite
文件存储和SharedPreferences存储只适用于去保存一些简单的数据和键值对，当需要存储大量复杂的关系型数据的时候，这两种存储方式就很难应付了。

![程序界面](http://upload-images.jianshu.io/upload_images/1917079-fe6b0e1352d0b69c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

###1.创建数据库
Android为了让我们更加方便的管理数据库，专门提供了一个SQLiteOpenHelper帮助类，借助这个类可以非常简单地对数据库进行创建和升级。SQLiteOpenHelper是一个抽象类，我们要使用的话需要先创建一个自己的帮助类去继承它，而且必须在我们的帮助类重写onCreate()和onUpgrade()这两个方法，然后分别在这两个方法中去实现创建、升级数据库的逻辑。

SQLiteOpenHelper有两个非常重要的实例方法，getReadableDatabase()和getWritableDatabase()。这两个方法都可以创建或者打开一个现有的数据库，并返回一个可以数据库进行读写操作的对象。不同的是，当数据库不可写入的时候（如磁盘空间已满）getReadableDatabase()方法返回的对象将以只读的方式去打开数据库，而 getWritableDatabase()方法则将出现异常。

SQLiteOpenHelper有两个构造方法可以重写，一般使用参数少一点的那个，这个构造方法有四个参数，第一个是Context，第二个是数据库名，第三个是允许我们在查询数据的时候返回一个自定义的 Cursor，一般传入null，第四个表示当前数据库的版本号，用于升级数据库。实例化SQLiteOpenHelper对象后，调用它的getReadableDatabase()或getWritableDatabase()方法就能创建出数据库了，数据库文件存放于/data/data/<package name>/databases/目录下。

在新建项目之前先来看一下SQL的建表语句
```SQLite
create table Book(id integer primary key autoincrement,name text,author text,pages integer,price real)
```
SQLite的数据类型比较简单，integer表示整形，real表示浮点型，text表示文本类型，blob表示二进制类型。另外上述建表语句还使用primary key将id设为主键，autoincrement将id列设为自动增长的。

接下来新建一个DatabaseTest项目来看看如何闯创建数据库吧。首先在布局中放一个Button用来创建数据库，这部分就不展示出来了。然后创建MyDatabaseHelper类继承自SQLiteOpenHelper，代码如下：
```java
public class MyDatabaseHelper extends SQLiteOpenHelper {   
 
    private static final String CREATE_BOOK = "create table Book(" +            
            "id integer primary key autoincrement," +            
            "name text," +            
            "author text," +            
            "pages integer," +            
            "price real)";    
    private Context context;    

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
首先把建表语句定义为字符串常量，然后在onCreate()方法中调用SQLiteDatabase的execSQL()方法来执行这条建表语句完成建表。然后在MainActivity的Button监听事件来完成数据库的创建，findViewById和为Button设置键监听器的部分就不写了，代码如下：
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private MyDatabaseHelper databaseHelper;
    private SQLiteDatabase db;

    @Override
    protected void onCreate(Bundle savedInstanceState) {    
        super.onCreate(savedInstanceState);    
        setContentView(R.layout.activity_main);    

        databaseHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);    
        ……
    }

    @Override
    public void onClick(View v) {    
      switch (v.getId()) {        
        case R.id.create_database:            
            db = databaseHelper.getWritableDatabase();            
            break;
        default:        
            break;
        }
    }
}

```
运行一下程序，创建完数据库之后就会弹出一个Toast来提醒。接下来使用ADB来验证数据库BookStore.db以及Book表的创建。
![](http://upload-images.jianshu.io/upload_images/1917079-13d5af8eaa6ba3c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)首先使用**adb shell**进入设备，然后进入**/data/data/<package name>/databases/**目录，再用**ls**查看目录下的文件，可以看到BookStore.db数据库已经创建出来了，另一个BookStore.db-journal 则是为了让数据库能够支持事务而产生的临时日志文件，可以不用管它。

然后用**sqlite3 BookStore.db**打开BookStore.db数据库（注意数据库名字不要打错了，如果你发现数据库里没有Book表，很有可能就是你打错了数据库的名字，这个时候用**.exit**或者**.quit**命令就可以关闭这个错的数据库了，再用**rm 数据库名**就可以删掉错误的数据库了）。

然后用**.table**命令（注意“.”）就可以看到该数据库下的表了。android_metadata 表是每个数据库中都会自动生成的，这个也不用管它。这里还可以通过**.schema **命令来查看它们的建表语句。

###2.在数据库中新建一个表
如果要新建一个表的话，与前面的方法一样，首先需要在MyDatabaseHelper类把建表语句定义为字符串常量，然后在onCreate中完成建表。
```java
public class MyDatabaseHelper extends SQLiteOpenHelper {    
    ……
    private static final String CREATE_CATEGORY = "create table Category(" +            
        "id integer primary key autoincrement," +            
        "category_name text," +            
        "category_cade integer)";    
    ……
    @Override    
    public void onCreate(SQLiteDatabase db) {        
        db.execSQL(CREATE_BOOK);        
        db.execSQL(CREATE_CATEGORY);        
        Toast.makeText(context, "Database Created succeeded", Toast.LENGTH_SHORT).show();    
}   

    @Override    
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {        

    }
}
```
重新运行一下程序点击Button可以发现并不会有Toast弹出来，用ADB工具也差看不到Category这张表。原因就在SQLiteOpenHelper的getWritableDatabase()只有数据库不存在的时候才会创建数据库并调用MyDatabaseHelper的onCreate方法，现在数据库已经存在，所以无论我们怎么点“Create Database”都不会创建一个新的数据库。现在只有卸载这个程序或者利用ADB的**rm BookStore.db**命令将数据库删除后再创建新的数据库才会有Category表。

以上两种办法都有点暴力，需要上线的APP肯定不能用这样的方法，有没有其他的方法不这么暴力的呢？答案肯定是有的，利用升级这个办法我们就可以实现给数据库添加一张新表或者其他的操作。然后修改MyDatabaseHelper的onUpgrade方法。因为升级数据库时就会调用这个方法。
```java
@Override
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {    
    db.execSQL("drop table if exists Book");    
    db.execSQL("drop table if exists Category");    
    onCreate(db);
}
```
可以看到这里执行了两条drop语句，当Book表或Category表已经存在的话就会把这两张表删掉，然后再调用onCreate()方法重新建表。只所以在重新建表之前要删掉原来的表，是因为在创建表时如果已经存在，那么就会直接报错。接下来修改MainActivity的代码。
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    ……
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ……
        databaseHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
    }
    ……
}
```
**databaseHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2)**最后的版本号要比原来的版本号大，数据库才会进行升级。重新运行程序，再利用ADB工具来查看数据库里的内容。

![](http://upload-images.jianshu.io/upload_images/1917079-c85dd5a7cb8a6311.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)这次可以看到数据库升级之后，Category表也被创建出来了。现在数据库已经创建出来了，也完成了升级，但是Book表和Category表都没有数据，接下来就来看看怎么给Book表添加数据。
###3.添加数据
通过前面的练习，我们已经知道，调用SQLiteOpenHelper的getReadableDatabase()或 getWritableDatabase()方法是可以用于创建和升级数据库的，不仅如此，这两个方法还都会返回一个SQLiteDatabase对象，借助这个对象就可以对数据进行 CRUD 操作了。什么是CRUD操作？C就是Create（添加数据）；R就是Retrieve（查询数据）；U就是Update（更新数据）；D就是Delete（删除数据）。

接下来就先来看看怎么进行C操作吧。SQLiteDatabase中提供了一个insert()方法，这个方法就是专门用于添加数据的。它接收三个参数，第一个参数是表名，我们希望向哪张表里添加数据，这里就传入该表的名字。第二个参数用于在未指定添加数据的情况下给某些可为空的列自动赋值 null，一般我们用不到这个功能， 直接传入 null 即可。 第三个参数是一个ContentValues 对象， 它提供了一系列的 put()方法重载，用于向ContentValues 中添加数据，只需要将表中的每个列名以及相应的待添加数据传入即可。

接下来再布局中加入一个Insert Data按钮，在MainActivity中添加监听事件，代码如下：
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    ……
    private ContentValues values = new ContentValues();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ……
    }

    @Override
    public void onClick(View v) {    
        switch (v.getId()) {        
            ……
            case R.id.insert_data:    
                db = databaseHelper.getWritableDatabase();  
                values.clear();      
                //第一条数据    
                values.put("name", "The Da Vinci Code");    
                values.put("author", "Dan Brown");    
                values.put("pages", 454);    
                values.put("price", 16.96);    
                db.insert("Book", null, values);    
                values.clear();    
                //第二条数据    
                values.put("name", "The Lost Symbol");    
                values.put("author", "Dan Brown");    
                values.put("pages", 510);    
                values.put("price", 19.95);    
                db.insert("Book", null, values);    
                break;
            default:        
                break;
        }
    }
}
```
首先先实例化一个ContentValues对象，在insert数据之前先清空ContentValues的值，然后把要添加的数据放到ContentValues进行组装，这里并没有给id那一列赋值，因为id我们已经设置为自动增长的，它的值会在入库的时候自动生成，所以不需要我们手动赋值了，最后调用SQLiteDatabase的insert()方法将数据添加到对应的表中。这里实际上是添加了两条数据，因为调用了两次insert()方法，并且ContentValues组装的内容也不相同。

接下来运行程序，点击Insert Data按钮，再利用ADB工具查看效果。前面的命令都用过了就不过多解释了，**select * from Book;**是SQL命令，用来查询表中的数据，完整的命令格式是“select列名称from表名称 where 限定条件”，可以选择要查询的列中满足限定条件的数据。我们在列名称这里用*代替，说明查询所有列，而且不加限定条件就说明我们要查Book表的所有数据。

**注意在最后要加上“;”**，我就掉进过这个坑中，好一会才爬出来。

![](http://upload-images.jianshu.io/upload_images/1917079-45ff37bb83fb11ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以看到数据已经成功的条件到了Book表中了，接下来来看一下怎么进行U操作吧。
###4.更新数据
SQLiteDatabase中也是提供了一个非常好用的update()方法用于对数据进行更新，这个方法接收四个参数，第一个参数和insert()方法一样，也是表名，在这里指定去更新哪张表里的数据。第二个参数是ContentValues对象，要把更新数据在这里组装进去。第三、第四个参数用于去约束更新某一行或某几行中的数据，不指定的话默认就是更新所有行。

在布局中加入Updata data按钮，在MainActivity中给它添加监听事件。
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    ……

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ……
    }

    @Override
    public void onClick(View v) {    
        switch (v.getId()) {        
            ……
          case R.id.update_data:    
                db = databaseHelper.getWritableDatabase();    
                values.clear();    
                values.put("price", 10.99);    
                db.update("Book", values, "name = ?", new String[]{"The Da Vinci Code"});    
                break;
            default:        
                break;
        }
    }
}
```
这里还是使用同一个ContentValues，不过这次只放了一个price的数据。然后调用了SQLiteDatabase的update()方法去执行具体的更新操作，可以看到，这里使用了第三、第四个参数来指定具体更新哪几行。第三个参数对应的是SQL语句的where部分，表示去更新所有name等于?的行，而?是一个占位符，可以通过第四个参数提供的一个字符串数组为第三个参数中的每个占位符指定相应的内容。因此上述代码想表达的意图就是，将name为The Da Vinci Code的price改成10.99。

运行程序点击Updata data按钮，通过ADB工具查看效果。
![](http://upload-images.jianshu.io/upload_images/1917079-32aed039d68807cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以看到第一条数据也就是name为The Da Vinci Code的这条数据的price改为10.99了。数据的更新就到这里，接下来看一下D操作吧。

###5.删除数据
删除数据相比前面两种操作就更加简单了，SQLiteDatabase中提供了一个delete()方法专门用于删除数据，这个方法接收三个参数，第一个参数仍然是表名，第二、第三个参数又是用于去约束删除某一行或某几行的数据，不指定的话默认就是删除所有行。

添加Delete data 按钮，然后为其添加监听事件。
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    ……

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ……
    }

    @Override
    public void onClick(View v) {    
        switch (v.getId()) {        
            ……
            case R.id.delete_data:    
                db = databaseHelper.getWritableDatabase();    
                db.delete("Book", "pages>?", new String[]{"500"});    
                break;
            default:        
                break;
        }
    }
}
```
与更新数据一样，这里也是使用了where来修饰要删除的行，将第三个参数的值替换第二个参数的?占位符，代码表达的意思就是删除Book表中pages>500的那条数据。

运行程序点击Delete data 按钮，通过ADB工具查看效果。
![](http://upload-images.jianshu.io/upload_images/1917079-da55526b4b6821d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以看到pages为510的这条数据被删掉了，只剩下pages为454的这条数据。数据的删除就到这里，接下来看一下R操作吧。
###6.查询数据
 SQL 的全称是Structured Query Language，翻译成中文就是结构化查询语言。它的大部功能都是体现在“查”这个字上的，而“增删改”只是其中的一小部分功能。

SQLiteDatabase中还提供了一个query()方法用于对数据进行查询，我将使用两种查询方法来做演示。这个方法的参数非常复杂，最短的一个方法重载也需要传入七个参数。先来看一下这七个参数各自的含义吧。第一个参数不用说，当然还是表名，表示我们希望从哪张表中查询数据。第二个参数用于指定去查询哪几列，如果不指定则默认查询所有列。第三、第四个参数用于去约束查询某一行或某几行的数据，不指定则默认是查询所有行的数据。第五个参数用于指定需要去group by的列，不指定则表示不对查询结果进行group by操作。第六个参数用于对group by之后的数据进行进一步的过滤，不指定则表示不进行过滤。第七个参数用于指定查询结果的排序方式，不指定则表示使用默认的排序方式。

第二种方法跟第一种差别不大，我就不这里赘述了，直接在代码中以注释的方式来解释每个参数。

首先添加Query data 按钮，然后为其添加监听事件。
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    ……
    private String Tag = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ……
    }

    @Override
    public void onClick(View v) {    
        switch (v.getId()) {        
            ……
            case R.id.query_data:                
                db = databaseHelper.getWritableDatabase();                
                /**                 
                * 查询指定表，返回一个Cursor对象。                 
                * @param table     要查询的表名。                 
                * @param columns   要查询的列名，如果传入null将会查询所有的列                 
                * @param selection 指定Where的约束条件，用于约束查询某一列或某几列的数据，传入空将会返回所有列。                 
                * @param selectionArgs 将selectionArgs值逐个替换selection中的"?"占位符。                 
                * @param groupBy 控制列分组，传入null就不进行分组                 
                * @param having 对groupBy之后的数据进一步过滤，传入null则不进行过滤                 
                * @param orderBy 对查询后的数据进行排序，传入null就会默认排序，也可能是无序的。                 
                * @return 返回一个Cursor对象                 
                */                
                Cursor cursor = db.query("Book", null, null, null, null, null, null);                


                /**                 
                * @param distinct 传入true就会去除重复的数据，传去false则反之。                 
                * @param table The table name to compile the query against.                 
                * @param columns A list of which columns to return. Passing null will                 
                *            return all columns, which is discouraged to prevent reading                 
                *            data from storage that isn't going to be used.                 
                * @param selection A filter declaring which rows to return, formatted as an                 
                *            SQL WHERE clause (excluding the WHERE itself). Passing null                 
                *            will return all rows for the given table.                 
                * @param selectionArgs You may include ?s in selection, which will be                 
                *         replaced by the values from selectionArgs, in order that they                 
                *         appear in the selection. The values will be bound as Strings.                 
                * @param groupBy A filter declaring how to group rows, formatted as an SQL                 
                *            GROUP BY clause (excluding the GROUP BY itself). Passing null                 
                *            will cause the rows to not be grouped.                 
                * @param having A filter declare which row groups to include in the cursor,                 
                *            if row grouping is being used, formatted as an SQL HAVING                 
                *            clause (excluding the HAVING itself). Passing null will cause                 
                *            all row groups to be included, and is required when row                 
                *            grouping is not being used.                 
                * @param orderBy How to order the rows, formatted as an SQL ORDER BY clause                 
                *            (excluding the ORDER BY itself). Passing null will use the                 
                *            default sort order, which may be unordered.                 
                * @param limit 指明返回的rows的数量。                 
                * @return A {@link Cursor} object, which is positioned before the first entry. Note that                 
                * {@link Cursor}s are not synchronized, see the documentation for more details.                 
                * @see Cursor                 
                */
//                Cursor cursor = db.query(true,"Book",new String[]{"name","author","price"},"price<?",new String[]{"15"},null,null,null,null);                

                if (cursor.moveToFirst()) {                    
                    do {                        
                        String name = cursor.getString(cursor.getColumnIndex("name"));                        
                        String author = cursor.getString(cursor.getColumnIndex("author"));                        
                        int pages = cursor.getInt(cursor.getColumnIndex("pages"));                        
                        double price = cursor.getDouble(cursor.getColumnIndex("price"));                        
                        Log.d(Tag, "book name is " + name);                        
                        Log.d(Tag, "book author is " + author);                        
                        Log.d(Tag, "book pages is " + pages);                        
                        Log.d(Tag, "book price is " + price);                    
                    } while (cursor.moveToNext());                
                }                
                cursor.close();                
                break;
            default:        
                break;
        }
    }
}
```
在这里我将两种查询数据的方法都写在一起，这样大家容易对比两个方法有什么不同，当然查询数据的方法不知这两个，而且我给了这两个方法的注释，第一个方法用的是中文注释（英文不好，有弄错的地方希望大神可以指正），第二个方法我用的是源代码中的注释，但是有两个参数是第一个方法中没有的，我就用中文注释了。

先注释掉第二个方法，来看看第一个查询数据的结果是怎么样的。在查询按钮的点击事件里面调用了SQLiteDatabase的query()方法去查询数据。这里的query()方法非常简单，只是使用了第一个参数指明去查询Book表，后面的参数全部为null。这就表示希望查询这张表中的所有数据，虽然这张表中目前只剩下一条数据了。查询完之后就得到了一个Cursor对象，接着我们调用它的moveToFirst()方法将数据的指针移动到第一行的位置，然后进入了一个循环当中，去遍历查询到的每一行数据。在这个循环中可以通过Cursor的getColumnIndex()方法获取到某一列在表中对应的位置索引，然后将这个索引传入到相应的取值方法中，就可以得到从数据库中读取到的数据了。接着我们使用Log的方式将取出的数据打印出来，借此来检查一下读取工作有没有成功完成。最后别忘了调用close()方法来关闭Cursor。


现在我们的数据库只有一条数据，按下Insert data添加多两条数据再按下Query data按钮来查询数据，看Android Studio的控制台输出就可以知道查询的结果了。
![](http://upload-images.jianshu.io/upload_images/1917079-007b870cc2e13573.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)不难看出一共是有3条数据。我们再用ADB工具来验证一下是不是3条数据。
![](http://upload-images.jianshu.io/upload_images/1917079-b366005f7ce5c475.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以看到也是只有3条数据，id为2的已经被我们给删除了。所以使用SQLiteDatabase的query()来查询数据完全是没有问题的。

接下来看看第二种查询方法会得出什么结果。首先注释掉第一种方法，然后再取消注释第二个方法，重新run一下。再按下Query data按钮，程序居然崩掉了，看一下Log。

![](http://upload-images.jianshu.io/upload_images/1917079-fbc8840270c069c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)这个异常是非法状态异常，后半句提示我们在访问数据之前对指针正确地初始化。然后MainActivity.java:124跳转到出错的代码。
![](http://upload-images.jianshu.io/upload_images/1917079-bcf2b2dffe9eb229.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以看到我们在查询数据的时候并没有查询pages这一列，但是我们在后面使用**int pages = cursor.getInt(cursor.getColumnIndex("pages"));**从cursor中取出pages的值所以导致程序崩溃，将这句代码以及相应的Log注释掉即可。然后重新运行程序。
![](http://upload-images.jianshu.io/upload_images/1917079-3bd9044f2faba5cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以看到只有一条数据，因为我们加了一条price<15的条件，接下来我们把15改成20，然后运行程序，再insert几条数据进去。
![](http://upload-images.jianshu.io/upload_images/1917079-8ff5c594317bed59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)我们可以从ADB工具看到一共是由9条数据的(id=2的被删掉了)。然后按下Query data按钮，查看Log。
![](http://upload-images.jianshu.io/upload_images/1917079-9305cf2438f07090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)只有3条数据，而且没有pages这一列。因为我们第一个参数传进了一个true，所以查询结果会把重复的部分去除掉。我们在第三个参数中传入了new String[]{"name","author","price"}意思就是只查询name、author以及price这三列的数据，所以不会有pages的结果。

*在Android中同样可以使用SQL操作数据库，例如：*
![](http://upload-images.jianshu.io/upload_images/1917079-5e531c2298804e79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)*因为文章篇幅较长，就不再过多介绍了，有兴趣的话可以去查找SQL的相关资料学习。*


>如果文章对你有所帮助，那么请您点一下❤
>由于本人水平有限，如有错误，欢迎大家指正。如果你在操作过程中发现一些没有讲到的错误或者问题，欢迎在评论留言，一起探讨，共同学习进步！
