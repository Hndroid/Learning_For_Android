### android下数据库的创建方式（一）

---

#### 创建数据库的一般步骤：

1. 创建一个类，继承SqliteOpenHelper，然后重写onCreate\(\)、onUpGrade\(\)这两个方法;

```java
public class MySqliteOpenHelper extends SQLiteOpenHelper {

    /*
    * context:上下文;
    * name：数据文件的名称;
    * factory:用来创建cursor对象;
    * version：数据库的版本号，从 1 开始，版本号如果发生改变  ，onUpgrade()方法会被调用;
    * */
    public MySqliteOpenHelper(Context context) {
        super(context, "info.db", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        //在数据库文件第一次创建的时候会回调该方法,适合做表结构的初化
        //需要执行sql语句;SQLiteDateBase db可以用来执行sql语句;
        db.execSQL("CREATE TABLE info(_id INTEGER PRIMARY KEY AUTOINCREMENT, 
        name VARCHAR(20), age VARCHAR(20))");

    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        //数据库发生改变的时候会回调该方法;特别适合表结构发生改变;
    }
}
```

2.创建这个继承于帮助类的一个对象，调用getReadableDatabase\(\)方法，会帮助我们创建打开一个数据库;

```java
 MySqliteOpenHelper mySqliteOpenHelper = new MySqliteOpenHelper(context);
 SQLiteDatabase db = mySqliteOpenHelper.getWritableDatabase();
```

3.创建数据后之后，可以对SQLite建立的表进行操作：

对数据库进行基本操作的基本思路：获取继承于SQLiteOpenHelper的对象，然后通过getWritableDatabase\(\)方法得到一个

SQLiteDatabase类型的返回值，从而对数据库进行基本的操作;

* **对数据库进行添加数据**

```java
 //数据库的添加操作
    public void add(ArrayList<SqliteTableBean> arrayList) {
        for (int i = 0; i < arrayList.size(); i++) {
            sqliteTableBean.name = arrayList.get(i).name;//实体类
            sqliteTableBean.phone = arrayList.get(i).phone;//实体类
            SQLiteDatabase database = mySqliteOpenHelper.getWritableDatabase();
            database.execSQL("insert into info(name, phone) values(?, ?)", new Object[]{sqliteTableBean.name,
                    sqliteTableBean.phone});
            database.close();//关闭操作;
        }
    }
```

* **对数据库进行查询数据（适合做多表查询）**

```java
//数据库的查找
    public void querry(String name) {
        //调用getgetWritableDatabase()方法，来初始化数据库的创建;
        SQLiteDatabase database = mySqliteOpenHelper.getWritableDatabase();
        /*
        * 参数：
        * String sql：sql语句
        * String[] selectionArgs：查询条件占位的值，返回一个查询光标
        * */
        Cursor cursor = database.rawQuery("select _id, name, phone from info where name = ?"
                , new String[]{name});
        //解析cursor中的数据
        if (cursor.moveToNext() && cursor.getCount() > 0) {//判断cursor中是否存在数据
            while (cursor.moveToNext()) {
                int id = cursor.getInt(0);
                String name_table = cursor.getString(1);
                String phone = cursor.getString(2);

                Toast.makeText(context, "_id:" + id + " name:" + name + " phone:" + phone
                        , Toast.LENGTH_SHORT).show();
            }
        }
```

* **对数据库进行删除数据**

```java
//数据库的删除
    public void delete(String sqliteTableBean) {
        SQLiteDatabase database = mySqliteOpenHelper.getWritableDatabase();
        database.execSQL("delete from info where name = ?", new Object[]{sqliteTableBean});
    }
```

_**源码示例：**_[_**https://github.com/UserBai /FirstMVP.git**_](https://github.com/UserBai/FirstMVP.git)

---

---

### Android下创建数据库\(二\)

### 创建数据库的一般方式：

1. 创建一个继承于帮助类的对象，调用getReadablieDataBase\(\)方法，返回一个SqliteDatabase对象;
2. 使用SqliteDatabase对象调用insert\(\)  update\(\)  delete\(\)  query\(\) 方法进行对数据库的操作;

> 特点:增删改有了返回值，可以判断sql语句是否执行成功，但是查询不够灵活，不能做多表查询。所以在
>
> 一般增删改用第二种方式，查询用第一种方式

```java
public InfoDao(Context context){
        //创建一个帮助类对象
        mySqliteOpenHelper = new MySqliteOpenHelper(context);
    }

    public boolean add(InfoBean bean){

        //执行sql语句需要sqliteDatabase对象
        //调用getReadableDatabase方法,来初始化数据库的创建
        SQLiteDatabase     db = mySqliteOpenHelper.getReadableDatabase();


        ContentValues values = new ContentValues();//是用map封装的对象，用来存放值
        values.put("name", bean.name);
        values.put("phone", bean.phone);

        //table: 表名 , nullColumnHack：可以为空，标示添加一个空行, values:数据一行的值 
        //, 返回值：代表添加这个新行的Id ，-1代表添加失败
        long result = db.insert("info", null, values);//底层是在拼装sql语句

        //关闭数据库对象
        db.close();

        if(result != -1){//-1代表添加失败
            return true;
        }else{
            return false;
        }
    }

    public int del(String name){

        //执行sql语句需要sqliteDatabase对象
        //调用getReadableDatabase方法,来初始化数据库的创建
        SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();

        //table ：表名, whereClause: 删除条件, whereArgs：条件的占位符的参数 ; 返回值：成功删除多少行
        int result = db.delete("info", "name = ?", new String[]{name});
        //关闭数据库对象
        db.close();

        return result;

    }
    public int update(InfoBean bean){

        //执行sql语句需要sqliteDatabase对象
        //调用getReadableDatabase方法,来初始化数据库的创建
        SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
        ContentValues values = new ContentValues();//是用map封装的对象，用来存放值
        values.put("phone", bean.phone);
        //table:表名, values：更新的值, whereClause:更新的条件, whereArgs：更新条件的占位符的值,返回值：成功修改多少行
        int result = db.update("info", values, "name = ?", new String[]{bean.name});
        //关闭数据库对象
        db.close();
        return result;

    }
    public void query(String name){

        //执行sql语句需要sqliteDatabase对象
        //调用getReadableDatabase方法,来初始化数据库的创建
        SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();

        //table:表名, columns：查询的列名,如果null代表查询所有列； selection:查询条件,
        // selectionArgs：条件占位符的参数值,
        //groupBy:按什么字段分组, having:分组的条件, orderBy:按什么字段排序
        Cursor cursor = db.query("info", new String[]{"_id","name","phone"}, "name = ?"
        , new String[]{name}, null, null, "_id desc");
        //解析Cursor中的数据
        if(cursor != null && cursor.getCount() >0){//判断cursor中是否存在数据

            //循环遍历结果集，获取每一行的内容
            while(cursor.moveToNext()){//条件，游标能否定位到下一行
                //获取数据
                int id = cursor.getInt(0);
                String name_str = cursor.getString(1);
                String phone = cursor.getString(2);
                System.out.println("_id:"+id+";name:"+name_str+";phone:"+phone);


            }
            cursor.close();//关闭结果集

        }
        //关闭数据库对象
        db.close();

    }
```

---

---

### **Android数据库的事务：**

事务：执行多条SQL语句，要么同时成功，要么同时失败，不可以有的成功，有的失败

示例：

**银行转账**

```java
    //点击按钮执行该方法
    public void transtation(View v){
        //1.创建一个帮助类的对象
        BankOpenHelper bankOpenHelper = new BankOpenHelper(this);
        //2.调用数据库帮助类对象的getReadableDatabase创建数据库，初始化表数据，
        //获取一个SqliteDatabase对象去做转账（sql语句）
        SQLiteDatabase db = bankOpenHelper.getReadableDatabase();
        //3.转账,将李四的钱减200，张三加200
        db.beginTransaction();//开启一个数据库事务
        try {
            db.execSQL("update account set money= money-200 where name=?",new String[]{"李四"});
            int i = 100/0;//模拟一个异常
            db.execSQL("update account set money= money+200 where name=?",new String[]{"张三"});

            db.setTransactionSuccessful();//标记事务中的sql语句全部成功执行
        } finally {
            db.endTransaction();//判断事务的标记是否成功，如果不成功，回滚错误之前执行的sql语句 
        }
    }
```



