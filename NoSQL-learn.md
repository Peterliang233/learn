### NoSQL学习教程

#### 下载安装NoSQL非关系型数据库

`yay -S mongodb-bin ` 通过命令行下载相关的包即可安装，安装完之后输入命令`mongo -version`即可查看mongodb的版本信息

查看运行状态

`systemctl status mongodb.service`

重启

`systemctl restart mongodb`

停止

`systemctl stop mongodb`

重启服务

`systemctl start mongodb.service`

开机自启动

`system enable mongodb`

之后找到相关的在根目录下面的/usr/bin/目录下面执行./mongo即可成功进入mongodb数据库了

+ 添加帐号密码

  `use admin`

  `db.createUser({user:"peter",pwd:"",roles:"root"})`

  `db.auth("account","password")`

#### NoSQL的基本操作命令

首先这里要注意的是数据库的命令也是以`;`结尾的。

mongodb的数据模型是数据库->集合->文档

##### 数据库相关命令

+ 创建数据库同时切换到相关的数据库下面，这里没有数据库的话会创建一个新的数据库 `use 数据库名字`
+ 查看数据库 `show dbs;`

+ 删除数据库,首先切换到相关的数据库下面，然后执行 `db.dropDatabase();`

##### 集合相关命令

我们首先要进入一个数据库，然后就可以执行对集合的相关操作了。

+ 创建一个新的空集合 `db.createCollection(name,option)`;
  + name： 要创建的集合的名称
  + options： 可选参数，指定有关内存大小及索引的选项
+ 删除集合`db.collection_name.drop();` 删除选定的集合
+ 查看当前数据库的所有集合 `show collections`
+ 查看某一个集合下面的所有文档的情况，`db.collection_name().find()`或者`db.collection_name().find().pretty()`

##### 文档相关命令

+ 插入相关的文档，我们称之为集合，这里面集合的数据的格式都是采用的是一种BSON的格式，这是一种类似于JSON格式的数据。
+ 插入文档 `db.COLLECTION_NAME.insert(document);` 或者`db.COLLECTION_NAME.save(document);`

+ 这里插入数据的方法有两种，一种是直接通过`db.collection_name.insert()`进行插入，也可以先定义好一个集合变量，然后在进行插入

  ```
  db.employee.insert(
  {
  	"id": 1,
  	"name": 'peter'
  }
  );
  或者
  document = ({
  	"title": 'myblog',
  	"id": 1,
  	"name": 'peter'
  });
  db.collection_name.insert(document);
  以上两种方法的效果是一样的
  ```

+ 文档更新

  ```
  db.collection_name.update(
  	<query>,  //update的查询条件，类似于sql update查询内where后面的
  	<update>,  //update的对象和一些更新的操作符，可以理解为sql update后面的set部分
  	{
  		upset:<boolean>,
  		mutil:<boolean>,
  		writeConcern:<document>
  	}
  )
  exp: db.collection.update({'title':'golang'},{$set:{'title':'java'}})
  或者db.col.save()直接进行全部替换
  ```

  

+ 文档删除 `db.collection_name.remove(<query>,<justOne>)`
  + query 表示的是删除的条件
  + justOne表示删除的个数，如果不设置该参数那么表示删除所有匹配的文档。

+ 文档查询 `db.collection_name.find(<query>,<projection>)`或者使用`db.collection_name.find().pretty()` 这样是以一种更易读的方法进行查看。除了find操作之外，还有findOne表示返回一个文档。
  + query表示查询的条件，如果不设置，默认查询整个表。
  + projection 使用投影操作符返回指定的键，查询是返回文档中所有的键值。