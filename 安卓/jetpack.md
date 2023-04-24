### 使用事项

##### synchronize

关键字 用来加锁

```
public synchronized static RoomDB getInstance(Context context) 
```

##### allowMainThreadQueries()

不建议使用`allowMainThreadQueries()`，因为它可能会长时间锁定UI并触发ANR。

> - 我需要allowMainThreadQueries()，因为否则我会遇到运行时错误，我没有使用MutableLiveData，只是通过Dao访问数据库实体
> - 在CoroutineScope内，您不需要allowMainThreadQueries()并且不会收到任何异常，这就是我的观点。 那就是我使用的代码。 没有allowMainThreadQueries()。

##### fallbackToDestructiveMigration()

每一次修改数据库中的结构，都需要对版本号进行升级，如version。

.fallbackToDestructiveMigration()方法是破坏式的迁移，即将原来所有的数据都清空了。

如果不在乎数据，可以这么做，但是一般不建议这么做。

```
database = Room.databaseBuilder(context.getApplicationContext(),
        RoomDB.class,DATABASE_NAME)
        .allowMainThreadQueries()
        .fallbackToDestructiveMigration()
        .build();
```







### [Android Room with a View - Java](https://developer.android.google.cn/codelabs/android-room-with-a-view#0)

### ROOM三个主要组件

+ 数据库类
+ 数据实体
+ 数据访问对象（DAO）



单个数据实体和单个DAO的Room数据库实现实例

#### 数据实体

以下代码定义了一个 `User` 数据实体。`User` 的每个实例都代表应用数据库中 `user` 表中的一行。

```java
@Entity
public class User {
    @PrimaryKey
    public int uid;

    @ColumnInfo(name = "first_name")
    public String firstName;

    @ColumnInfo(name = "last_name")
    public String lastName;
}
```

#### 数据访问对象（DAO）

以下代码定义了一个名为 `UserDao` 的 DAO。`UserDao` 提供了应用的其余部分用于与 `user` 表中的数据交互的方法。

```java
@Dao
public interface UserDao {
    @Query("SELECT * FROM user")
    List<User> getAll();

    @Query("SELECT * FROM user WHERE uid IN (:userIds)")
    List<User> loadAllByIds(int[] userIds);

    @Query("SELECT * FROM user WHERE first_name LIKE :first AND " +
           "last_name LIKE :last LIMIT 1")
    User findByName(String first, String last);

    @Insert
    void insertAll(User... users);

    @Delete
    void delete(User user);
}
```

#### 数据库

以下代码定义了用于保存数据库的 `AppDatabase` 类。 `AppDatabase` 定义数据库配置，并作为应用对持久性数据的主要访问点。数据库类必须满足以下条件：

- 该类必须带有 [`@Database`](https://developer.android.google.cn/reference/kotlin/androidx/room/Database?hl=zh-cn) 注解，该注解包含列出所有与数据库关联的数据实体的 [`entities`](https://developer.android.google.cn/reference/kotlin/androidx/room/Database?hl=zh-cn#entities) 数组。
- 该类必须是一个抽象类，用于扩展 [`RoomDatabase`](https://developer.android.google.cn/reference/kotlin/androidx/room/RoomDatabase?hl=zh-cn)。
- 对于与数据库关联的每个 DAO 类，数据库类必须定义一个具有零参数的抽象方法，并返回 DAO 类的实例。

```java
@Database(entities = {User.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract UserDao userDao();
}
```

用法

定义数据实体、DAO 和数据库对象后，您可以使用以下代码创建数据库实例：

```java
AppDatabase db = Room.databaseBuilder(getApplicationContext(),
        AppDatabase.class, "database-name").build();
```

然后，您可以使用 `AppDatabase` 中的抽象方法获取 DAO 的实例，转而可以使用 DAO 实例中的方法与数据库进行交互：

```java
UserDao userDao = db.userDao();
List<User> users = userDao.getAll();
```



#### 使用实体定义数据

##### 实体详解

您可以将每个 Room 实体定义为带有 [`@Entity`](https://developer.android.google.cn/reference/kotlin/androidx/room/Entity?hl=zh-cn) 注解的类。Room 实体包含数据库中相应表中的每一列的字段，包括构成[主键](https://developer.android.google.cn/training/data-storage/room/defining-data?hl=zh-cn#primary-key)的一个或多个列。

```java
@Entity
public class User {
    @PrimaryKey
    public int id;

    public String firstName;
    public String lastName;
}
```

> **注意**：要保留某个字段，Room 必须拥有该字段的访问权限。您可以通过将某个字段设为公开或为其提供 getter 和 setter 方法，确保 Room 能够访问该字段。

默认情况下，Room 将类名称用作数据库表名称。如果您希望表具有不同的名称，请设置 `@Entity` 注解的 [`tableName`](https://developer.android.google.cn/reference/kotlin/androidx/room/Entity?hl=zh-cn#tablename) 属性。同样，Room 默认使用字段名称作为数据库中的列名称。如果您希望列具有不同的名称，请将 [`@ColumnInfo`](https://developer.android.google.cn/reference/kotlin/androidx/room/ColumnInfo?hl=zh-cn) 注解添加到该字段并设置 [`name`](https://developer.android.google.cn/reference/kotlin/androidx/room/ColumnInfo?hl=zh-cn#name) 属性。以下示例演示了表和列的自定义名称：

```java
@Entity(tableName = "users")
public class User {
    @PrimaryKey
    public int id;

    @ColumnInfo(name = "first_name")
    public String firstName;

    @ColumnInfo(name = "last_name")
    public String lastName;
}
```

> **注意**：SQLite 中的表和列名称**不区分大小写**。



##### 定义主键

每个 Room 实体都必须定义一个[主键](https://en.wikipedia.org/wiki/Primary_key)，用于唯一标识相应数据库表中的每一行。执行此操作的最直接方式是使用 [`@PrimaryKey`](https://developer.android.google.cn/reference/kotlin/androidx/room/PrimaryKey?hl=zh-cn) 为单个列添加注解：

```java
@PrimaryKey
public int id;
```

> **注意**：如果您需要 Room 为实体实例分配自动 ID，请将 `@PrimaryKey` 的 [`autoGenerate`](https://developer.android.google.cn/reference/kotlin/androidx/room/PrimaryKey?hl=zh-cn#autogenerate) 属性设为 `true`。

###### 定义复合主键

如果您需要通过多个列的组合对实体实例进行唯一标识，则可以通过列出 `@Entity` 的 [`primaryKeys`](https://developer.android.google.cn/reference/kotlin/androidx/room/Entity?hl=zh-cn#primarykeys) 属性中的以下列定义一个复合主键：

```java
@Entity(primaryKeys = {"firstName", "lastName"})
public class User {
    public String firstName;
    public String lastName;
}
```

##### 忽略字段

默认情况下，Room 会为实体中定义的每个字段创建一个列。 如果某个实体中有您不想保留的字段，则可以使用 [`@Ignore`](https://developer.android.google.cn/reference/androidx/room/Ignore?hl=zh-cn) 为这些字段添加注解，如以下代码段所示：

```java
@Entity
public class User {
    @PrimaryKey
    public int id;

    public String firstName;
    public String lastName;

    @Ignore
    Bitmap picture;
}
```

如果实体继承了父实体的字段，则使用 `@Entity` 属性的 [`ignoredColumns`](https://developer.android.google.cn/reference/androidx/room/Entity?hl=zh-cn#ignoredcolumns) 属性通常会更容易：

```java
@Entity(ignoredColumns = "picture")
public class RemoteUser extends User {
    @PrimaryKey
    public int id;

    public boolean hasVpn;
}
```

##### 提供表搜索支持

###### 支持全文搜索

如果您的应用需要通过全文搜索 (FTS) 快速访问数据库信息，请使用虚拟表（使用 FTS3 或 FTS4 [SQLite 扩展模块](https://www.sqlite.org/fts3.html)）为您的实体提供支持。如需使用 Room 2.1.0 及更高版本中提供的这项功能，请将 [`@Fts3`](https://developer.android.google.cn/reference/androidx/room/Fts3?hl=zh-cn) 或 [`@Fts4`](https://developer.android.google.cn/reference/androidx/room/Fts4?hl=zh-cn) 注解添加到给定实体，如以下代码段所示：

```java
// Use `@Fts3` only if your app has strict disk space requirements or if you
// require compatibility with an older SQLite version.
@Fts4
@Entity(tableName = "users")
public class User {
    // Specifying a primary key for an FTS-table-backed entity is optional, but
    // if you include one, it must use this type and column name.
    @PrimaryKey
    @ColumnInfo(name = "rowid")
    public int id;

    @ColumnInfo(name = "first_name")
    public String firstName;
}
```

> **注意**：启用 FTS 的表始终使用 `INTEGER` 类型的主键且列名称为“rowid”。如果是由 FTS 表支持的实体定义主键，则**必须**使用相应的类型和列名称。

如果表支持以多种语言显示的内容，请使用 `languageId` 选项指定用于存储每一行语言信息的列：

```java
@Fts4(languageId = "lid")
@Entity(tableName = "users")
public class User {
    // ...

    @ColumnInfo(name = "lid")
    int languageId;
}
```

Room 提供了用于定义由 FTS 支持的实体的其他几个选项，包括结果排序、令牌生成器类型以及作为外部内容管理的表。如需详细了解这些选项，请参阅 [`FtsOptions`](https://developer.android.google.cn/reference/androidx/room/FtsOptions?hl=zh-cn) 参考文档。

##### 添加基于 AutoValue 的对象



#### 使用RoomDAO访问数据

当您使用 Room 持久性库存储应用的数据时，您可以通过定义数据访问对象 (DAO) 与存储的数据进行交互。每个 DAO 都包含一些方法，这些方法提供对应用数据库的抽象访问权限。在编译时，Room 会自动为您定义的 DAO 生成实现。

通过使用 DAO（而不是查询构建器或直接查询）来访问应用的数据库，您可以[使关注点保持分离](https://developer.android.google.cn/jetpack/guide?hl=zh-cn#separation-of-concerns)，这是一项关键的架构原则。DAO 还可让您在[测试应用](https://developer.android.google.cn/training/data-storage/room/testing-db?hl=zh-cn)时更轻松地模拟数据库访问。

##### DAO 剖析

您可以将每个 DAO 定义为一个接口或一个抽象类。对于基本用例，您通常应使用接口。无论是哪种情况，您都必须始终使用 [`@Dao`](https://developer.android.google.cn/reference/kotlin/androidx/room/Dao?hl=zh-cn) 为您的 DAO 添加注解。DAO 不具有属性，但它们定义了一个或多个方法，可用于与应用数据库中的数据进行交互。

以下代码是一个简单 DAO 的示例，它定义了在 Room 数据库中插入、删除和选择 `User` 对象的方法：

```java
@Dao
public interface UserDao {
    @Insert
    void insertAll(User... users);

    @Delete
    void delete(User user);

    @Query("SELECT * FROM user")
    List<User> getAll();
}
```

有两种类型的 DAO 方法可以定义数据库交互：

- 可让您在不编写任何 SQL 代码的情况下插入、更新和删除数据库中行的便捷方法。
- 可让您编写自己的 SQL 查询以与数据库进行交互的查询方法。

以下部分演示了如何使用这两种类型的 DAO 方法定义应用所需的数据库交互。

##### 便捷方法

Room 提供了方便的注解，用于定义无需编写 SQL 语句即可执行简单插入、更新和删除的方法。

如果您需要定义更复杂的插入、更新或删除操作，或者需要查询数据库中的数据，请改用[查询方法](https://developer.android.google.cn/training/data-storage/room/accessing-data?hl=zh-cn#query)。

###### 插入

借助 [`@Insert`](https://developer.android.google.cn/reference/kotlin/androidx/room/Insert?hl=zh-cn) 注释，您可以定义将其参数插入到数据库中的相应表中的方法。以下代码展示了将一个或多个 `User` 对象插入数据库的有效 `@Insert` 方法示例：

```java
@Dao
public interface UserDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    public void insertUsers(User... users);

    @Insert
    public void insertBothUsers(User user1, User user2);

    @Insert
    public void insertUsersAndFriends(User user, List<User> friends);
}
```

`@Insert` 方法的每个参数必须是带有 `@Entity` 注解的 [Room 数据实体类](https://developer.android.google.cn/training/data-storage/room/defining-data?hl=zh-cn)的实例或数据实体类实例的集合。调用 `@Insert` 方法时，Room 会将每个传递的实体实例插入到相应的数据库表中。

如果 `@Insert` 方法接收单个参数，则会返回 `long` 值，这是插入项的新 `rowId`。如果参数是数组或集合，则该方法应改为返回由 `long` 值组成的数组或集合，并且每个值都作为其中一个插入项的 `rowId`。如需详细了解如何返回 `rowId` 值，请参阅 [`@Insert`](https://developer.android.google.cn/reference/kotlin/androidx/room/Insert?hl=zh-cn) 注解的参考文档以及 [rowid 表的 SQLite 文档](https://www.sqlite.org/rowidtable.html)。

###### 更新

借助 [`@Update`](https://developer.android.google.cn/reference/kotlin/androidx/room/Update?hl=zh-cn) 注释，您可以定义更新数据库表中特定行的方法。与 `@Insert` 方法类似，`@Update` 方法接受数据实体实例作为参数。 以下代码展示了一个 `@Update` 方法示例，该方法尝试更新数据库中的一个或多个 `User` 对象：

```java
@Dao
public interface UserDao {
    @Update
    public void updateUsers(User... users);
}
```

Room 使用[主键](https://developer.android.google.cn/training/data-storage/room/defining-data?hl=zh-cn#primary-key)将传递的实体实例与数据库中的行进行匹配。如果没有具有相同主键的行，Room 不会进行任何更改。

`@Update` 方法可以选择性地返回 `int` 值，该值指示成功更新的行数。

###### 删除

借助 [`@Delete`](https://developer.android.google.cn/reference/kotlin/androidx/room/Delete?hl=zh-cn) 注释，您可以定义从数据库表中删除特定行的方法。与 `@Insert` 方法类似，`@Delete` 方法接受数据实体实例作为参数。 以下代码展示了一个 `@Delete` 方法示例，尝试从数据库中删除一个或多个 `User` 对象：

```java
@Dao
public interface UserDao {
    @Delete
    public void deleteUsers(User... users);
}
```

Room 使用[主键](https://developer.android.google.cn/training/data-storage/room/defining-data?hl=zh-cn#primary-key)将传递的实体实例与数据库中的行进行匹配。如果没有具有相同主键的行，Room 不会进行任何更改。

`@Delete` 方法可以选择性地返回 `int` 值，该值指示成功删除的行数。

##### 查询方法

使用 [`@Query`](https://developer.android.google.cn/reference/kotlin/androidx/room/Query?hl=zh-cn) 注解，您可以编写 SQL 语句并将其作为 DAO 方法公开。使用这些查询方法从应用的数据库查询数据，或者需要执行更复杂的插入、更新和删除操作。

Room 会在编译时验证 SQL 查询。这意味着，如果查询出现问题，则会出现编译错误，而不是运行时失败。

###### 简单查询

以下代码定义了一个方法，该方法使用简单的 `SELECT` 查询返回数据库中的所有 `User` 对象：

```java
@Query("SELECT * FROM user")
public User[] loadAllUsers();
```

###### 返回表格列的子集

在大多数情况下，您只需要返回要查询的表中的列的子集。例如，您的界面可能仅显示用户的名字和姓氏，而不是该用户的每一条详细信息。为节省资源并简化查询的执行，您应只查询所需的字段。

借助 Room，您可以从任何查询返回简单对象，前提是您可以将一组结果列映射到返回的对象。例如，您可以定义以下对象来保存用户的名字和姓氏：

```java
public class NameTuple {
    @ColumnInfo(name = "first_name")
    public String firstName;

    @ColumnInfo(name = "last_name")
    @NonNull
    public String lastName;
}
```

然后，您可以从查询方法返回该简单对象：

```java
@Query("SELECT first_name, last_name FROM user")
public List<NameTuple> loadFullName();
```

Room 知道该查询会返回 `first_name` 和 `last_name` 列的值，并且这些值会映射到 `NameTuple` 类的字段中。如果查询返回的列未映射到返回的对象中的字段，则 Room 会显示一条警告。

###### 将简单参数传递给查询

大多数情况下，您的 DAO 方法需要接受参数，以便它们可以执行过滤操作。Room 支持在查询中将方法参数用作绑定参数。

例如，以下代码定义了一个返回特定年龄以上的所有用户的方法：

```java
@Query("SELECT * FROM user WHERE age > :minAge")
public User[] loadAllUsersOlderThan(int minAge);
```

您还可以在查询中传递多个参数或多次引用同一参数，如以下代码所示：

```java
@Query("SELECT * FROM user WHERE age BETWEEN :minAge AND :maxAge")
public User[] loadAllUsersBetweenAges(int minAge, int maxAge);

@Query("SELECT * FROM user WHERE first_name LIKE :search " +
       "OR last_name LIKE :search")
public List<User> findUserWithName(String search);
```

###### 将一组参数传递给查询

某些 DAO 方法可能要求您传入数量不定的参数，参数的数量要到运行时才知道。Room 知道参数何时表示集合，并根据提供的参数数量在运行时自动将其展开。

例如，以下代码定义了一个方法，该方法返回了部分地区的所有用户的相关信息：

```java
@Query("SELECT * FROM user WHERE region IN (:regions)")
public List<User> loadUsersFromRegions(List<String> regions);
```



###### 查询多个表

您的部分查询可能需要访问多个表格才能计算出结果。您可以在 SQL 查询中使用 `JOIN` 子句来引用多个表。

以下代码定义了一种方法将三个表联接在一起，以便将当前已出借的图书返回给特定用户：

```java
@Query("SELECT * FROM book " +
       "INNER JOIN loan ON loan.book_id = book.id " +
       "INNER JOIN user ON user.id = loan.user_id " +
       "WHERE user.name LIKE :userName")
public List<Book> findBooksBorrowedByNameSync(String userName);
```

此外，您还可以定义简单对象以从多个联接表返回列的子集，如[返回表格列的子集](https://developer.android.google.cn/training/data-storage/room/accessing-data?hl=zh-cn#return-subset)中所述。以下代码定义了一个 DAO，其中包含一个返回用户姓名和借阅图书名称的方法：

```java
@Dao
public interface UserBookDao {
   @Query("SELECT user.name AS userName, book.name AS bookName " +
          "FROM user, book " +
          "WHERE user.id = book.user_id")
   public LiveData<List<UserBook>> loadUserAndBookNames();

   // You can also define this class in a separate file, as long as you add the
   // "public" access modifier.
   static class UserBook {
       public String userName;
       public String bookName;
   }
}
```

###### 返回多重映射

在 Room 2.4 及更高版本中，您还可以通过编写返回[多重映射](https://en.wikipedia.org/wiki/Multimap)的查询方法来查询多个表中的列，而无需定义其他数据类。

请参考[查询多个表](https://developer.android.google.cn/training/data-storage/room/accessing-data?hl=zh-cn#multiple-tables)部分中的示例。您可以直接从您的查询方法返回 `User` 和 `Book` 的映射，而不是返回保存有 `User` 和 `Book` 实例配对的自定义数据类的实例列表。

```java
@Query(
    "SELECT * FROM user" +
    "JOIN book ON user.id = book.user_id"
)
public Map<User, List<Book>> loadUserAndBookNames();
```

查询方法返回多重映射时，您可以编写使用 `GROUP BY` 子句的查询，以便利用 SQL 的功能进行高级计算和过滤。例如，您可以修改 `loadUserAndBookNames()` 方法，以便仅返回已借阅的三本或更多图书的用户：

```java
@Query(
    "SELECT * FROM user" +
    "JOIN book ON user.id = book.user_id" +
    "GROUP BY user.name WHERE COUNT(book.id) >= 3"
)
public Map<User, List<Book>> loadUserAndBookNames();
```

如果您不需要映射整个对象，还可以通过在查询方法的 [`@MapInfo`](https://developer.android.google.cn/reference/kotlin/androidx/room/MapInfo?hl=zh-cn) 注解中设置 [`keyColumn`](https://developer.android.google.cn/reference/kotlin/androidx/room/MapInfo?hl=zh-cn#keycolumn) 和 [`valueColumn`](https://developer.android.google.cn/reference/kotlin/androidx/room/MapInfo?hl=zh-cn#valuecolumn) 属性，返回查询中特定列之间的映射：

```java
@MapInfo(keyColumn = "userName", valueColumn = "bookName")
@Query(
    "SELECT user.name AS username, book.name AS bookname FROM user" +
    "JOIN book ON user.id = book.user_id"
)
public Map<String, List<String>> loadUserAndBookNames();
```



##### 特殊返回值类型

Room 提供了一些与其他 API 库集成的特殊返回值类型。

###### 使用 Paging 库将查询分页

Room 通过与 [Paging 库](https://developer.android.google.cn/topic/libraries/architecture/paging?hl=zh-cn)集成来支持分页查询。在 `Room 2.3.0-alpha01` 及更高版本中，DAO 可以返回 [`PagingSource`](https://developer.android.google.cn/reference/kotlin/androidx/paging/PagingSource?hl=zh-cn) 对象以便与 [Paging 3](https://developer.android.google.cn/topic/libraries/architecture/paging/v3-overview?hl=zh-cn) 搭配使用。

###### 直接光标访问

如果应用的逻辑要求直接访问返回行，您可以编写 DAO 方法以返回 [`Cursor`](https://developer.android.google.cn/reference/kotlin/android/database/Cursor?hl=zh-cn) 对象，如以下示例所示：

```java
@Dao
public interface UserDao {
    @Query("SELECT * FROM user WHERE age > :minAge LIMIT 5")
    public Cursor loadRawUsersOlderThan(int minAge);
}
```

> **注意**：强烈建议不要使用 Cursor API，因为它无法保证行是否存在或行包含哪些值。只有当您已具有需要光标且无法轻松重构的代码时，才使用此功能。







#### Android Room with a View

##### Create an entity

```
@Entity(tableName = "word_table")
public class Word {

   @PrimaryKey
   @NonNull
   @ColumnInfo(name = "word")
   private String mWord;

   public Word(@NonNull String word) {this.mWord = word;}

   public String getWord(){return this.mWord;}
}
```

- `@Entity(tableName =` **`"word_table"`**`)` Each `@Entity` class represents a SQLite table. Annotate your class declaration to indicate that it's an entity. You can specify the name of the table if you want it to be different from the name of the class. This names the table "word_table".
- `@PrimaryKey` Every entity needs a primary key. To keep things simple, each word acts as its own primary key.
- `@NonNull` Denotes that a parameter, field, or method return value can never be null.
- `@ColumnInfo(name =` **`"word"`**`)` Specify the name of the column in the table if you want it to be different from the name of the member variable.

**Tip:** You can [autogenerate](https://developer.android.google.cn/reference/androidx/room/PrimaryKey.html) unique keys by annotating the primary key as follows:

```
@Entity(tableName = "word_table")
public class Word {

    @PrimaryKey(autoGenerate = true)
    private int id;

    @NonNull
    private String word;
    //..other fields, getters, setters
}
```

##### Create the DAO

The DAO must be an interface or abstract class. By default, all queries must be executed on a separate thread.//DAO 必须是接口或抽象类。默认情况下，所有查询都必须在单独的线程上执行。

Let's write a DAO that provides queries for:

- Getting all words ordered alphabetically
- Inserting a word
- Deleting all words

1. Create a new class file called `WordDao`.
2. Copy and paste the following code into `WordDao` and fix the imports as necessary to make it compile:

```
@Dao
public interface WordDao {

   // allowing the insert of the same word multiple times by passing a 
   // conflict resolution strategy
   @Insert(onConflict = OnConflictStrategy.IGNORE)
   void insert(Word word);

   @Query("DELETE FROM word_table")
   void deleteAll();

   @Query("SELECT * FROM word_table ORDER BY word ASC")
   List<Word> getAlphabetizedWords();
}
```

Let's walk through it:

- `WordDao` is an interface; DAOs must either be interfaces or abstract classes.
- The `@Dao` annotation identifies it as a DAO class for Room.
- `void insert(Word word);` Declares a method to insert one word:
- The [`@Insert` annotation](https://developer.android.google.cn/reference/androidx/room/Insert) is a special DAO method annotation where you don't have to provide any SQL! (There are also [`@Delete`](https://developer.android.google.cn/reference/androidx/room/Delete) and [`@Update`](https://developer.android.google.cn/reference/androidx/room/Update) annotations for deleting and updating rows, but you are not using them in this app.)
- `onConflict = OnConflictStrategy.IGNORE`: The selected on conflict strategy ignores a new word if it's exactly the same as one already in the list. To know more about the available conflict strategies, check out the [documentation](https://developer.android.google.cn/reference/androidx/room/OnConflictStrategy.html).
- `deleteAll():` declares a method to delete all the words.
- There is no convenience annotation for deleting multiple entities, so it's annotated with the generic `@Query`.
- **`@Query("DELETE FROM word_table"):`** `@Query` requires that you provide a SQL query as a string parameter to the annotation.
- **`List<Word> getAlphabetizedWords():`** A method to get all the words and have it return a `List` of `Words`.
- `@Query(`**`"SELECT \* FROM word_table ORDER BY word ASC"`**`)`: Returns a list of words sorted in ascending order.