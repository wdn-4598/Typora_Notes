# 1. 简介

MongoDB是一个开源、高性能（C++编写）、基于分布式的文档型数据库，是NoSQL数据库产品中的一种。

先来看看SQL和MongoDB术语的对应关系，方便我们对MongoDB有一个大概的了解。

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                                                    |
| ------------ | ---------------- | ------------------------------------------------------------ |
| database     | database         | 数据库                                                       |
| table        | collection       | 数据库表/集合                                                |
| row          | document         | 数据记录(行)/文档                                            |
| column       | field            | 数据字段/域                                                  |
| index        | index            | 索引                                                         |
| table joins  |                  | 表连接，MongoDB不支持                                        |
|              | 嵌入文档         | MongoDB通过嵌入式文档来替代多表连接<br/>（查询效率要比表连接要高） |
| primary key  | primary key      | 主键，MongoDB自动将_id字段设置为主键                         |

## 1.1 数据模型

> 

| 数据类型       | 描述                                                         | 举例                                                         |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 字符串         | UTF-8字符串都可表示为字符串类型的数据                        | {“x” : “foobar”}                                             |
| 对象id         | 对象id是文档的12字节的唯一ID                                 | {“X” :ObjectId() }                                           |
| 布尔值         | 真或者假：true或者false                                      | {“x”:true}                                                   |
| 数组           | 值的集合或者列表可以表示成数组                               | {“x” ： [“a”, “b”, “c”]}                                     |
| 32位整数       | 类型不可用。JavaScript仅支持64位浮点数，所以32位整数会被自动转换。 | shell是不支持该类型的，shell中默认会转换成64位浮点数         |
| 64位整数       | 不支持这个类型。shell会使用一个特殊的内嵌文档来显示64位整数  | shell是不支持该类型的，shell中默认会转换成64位浮点数         |
| 64位浮点数     | shell中的数字就是这一种类型                                  | {“x”：3.14159，”y”：3}                                       |
| null           | 表示空值或者未定义的对象                                     | {“x”:null}                                                   |
| 符号           | shell不支持，shell会将数据库中的符号类型的数据自动转换成字符串 |                                                              |
| 正则表达式     | 文档中可以包含正则表达式，采用JavaScript的正则表达式语法     | {“x” ： /foobar/i}                                           |
| 代码           | 文档中还可以包含JavaScript代码                               | ~~{“x” ： function() { /* …… */ }}~~<br />Deprecated in MongoDB 4.4 |
| 二进制数据     | 二进制数据可以由任意字节的串组成，不过shell中无法使用        |                                                              |
| 最大值/最 小值 | BSON包括一个特殊类型，表示可能的最大值。shell中没有这个类型。 |                                                              |

-----


数据库分类：

- 关系型数据库（RDBMS）
  - 关系型数据库中全都是表
  - MySQL、Oracle、DB2
- 非关系型数据库（NoSQL Not Only SQL）
  - 键值对数据库
  - 文档数据库 MongoDB

# MongoDB

## 1. 简介

MongoDB的数据模型是面向文档的，所谓文档是一种类似JSON的结构。简单理解MongoDB数据库，它存的是各种各样的JSON（BSON）。

三个概念：

- 数据库；
- 集合（collection）：类似于数组，在collection中可以存放document；
- 文档（document）
  - 文档数据库中的最小存储单位，我们存储和操作的内容都是文档；
  - 由字段和值对（`filed:value`）组成，字段的数据类型是`字符型`，它的值除了使用基本的一些类型外，还可以包括`其他文档`、`普通数组`和`文档数组`。


> 数据在MongoDB中以 BSON（Binary-JSON）文档的格式存储在磁盘上。BSON（Binary Serialized Document Format）是一种类json的一种二进制形式的存储格式，简称Binary JSON。BSON和JSON一样，支持内嵌的文档对象和数组对象，但是BSON有JSON没有的一些数据类型，如Date和BinData类型。

## 2. 单机部署（Windows为例）

### 2.1 下载

安装MongoDB，两点注意：

- 根据业界规则，MongoDB的版本命名规范如：x.y.z；
  - y为奇数时表示当前版本为开发版，如：1.5.2、4.1.13；
  - y为偶数时表示当前版本为稳定版，如：1.6.3、4.0.10；
  - z是修正版本号，数字越大越好。
- 32bit的MongoDB最大只能存放2G的数据，64bit就没有限制；
- MongoDB对于32位系统支持不佳，所以3.2版本以后没有再对32位系统的支持。

到 [官网](https://www.mongodb.com/try/download/community) 下载，下载完成后解压zip文件。

![下载MongoDB](images/MongoDB/image-20220112204936629.png)

![解压](images/MongoDB/image-20220113123022838.png)

### 2.2 准备工作

在启动之前需要先做一些准备：

- 随便找个地方创建文件夹 `data/db` 和文件夹 `data/log`，db文件夹就是默认的数据库存放位置。推荐在MongoDB根目录下创建。
- ~~添加环境变量：在path中添加`D:\OfficeSoftware\mongodb-win32-x86_64-windows-5.0.5\bin`~~（新版不用）

![手动创建文件夹](images/MongoDB/image-20220113130541324.png)

### 2.3 启动服务器

在MongoDB的`bin`目录下打开cmd，输入命令:

```shell
mongod --dbpath=..\data\db --port=27017
```

- `dbpath`指定数据库存放位置，也是刚我们创建的db文件夹所在路径
- `port`指定端口号，默认端口号是27017，端口号尽量4位以上，4位以下的容易跟系统需要用到的端口冲撞 最大不要超过65535。

输入mongod启动 MongoDB服务器成功，也表示我们已经安装MongoDB成功。

![启动服务器](images/MongoDB/image-20220113125349677.png)

---

如若觉得每次输入命令太长太麻烦，可以把参数写到配置文件里，具体做法如下：

1. 在MongoDB的根目录下新建文件夹`config` (config文件夹与bin文件夹同级)

2. 在config文件夹里创建`mongod.conf`文件，文件内容要符合`YAML`语法格式，内容如下：

   ```yaml
   storage:
   #The directory where the mongod instance stores its data.Default Value is "\data\db" on Windows.
     dbPath: D:\OfficeSoftware\mongodb-win32-x86_64-windows-5.0.5\data\db
     
   net:
     #bindIp: 127.0.0.1
     port: 27017
   ```

3. `bin`目录下打开cmd，输入以下命令即可启动服务器

   ```shell
   mongod -f ../config/mongod.conf
   # 或者
   mongod --config ../config/mongod.conf
   ```

   

### 2.4 连接数据库

**启动服务器成功后，记得不要关掉cmd窗口，否则会连着服务器一起关闭掉！！！**

依然是在`bin`目录下，新打开一个cmd窗口：输入

```shell
mongo
```

![连接成功](images/MongoDB/image-20220113130111306.png)

如上图所示即表示连接成功。

## 3. 指令

### 3.1 基本指令

在MongoDB中，数据库和集合都不需要手动创建。当我们创建文档时，如果文档所在的集合或数据库不存在，mongo服务器会自动创建数据库和集合。

- 显示当前所有数据库：`show dbs` 或者 `show databses`
- 进入到指定数据库中：`use db_name`
- 当前所在的数据库：`db`，db表示的是当前所处的数据库
- 显示所在数据库中的所有集合：`show collections`

## 3.2 CRUD

官方文档：https://docs.mongodb.com/manual/crud/

- 增
  - `db.collection_name.insert()`
- 删
- 查
- 改

