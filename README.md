# mongodb

### 安装MongoDB

去官网下载：http://www.mongodb.org/downloads

由于是在window下，所以我下载的是msi格式

#### 配置

##### 创建数据目录
MongoDB将数据目录存储在 db 目录下。但是这个数据目录不会主动创建，我们在安装完成后需要创建它。

我的目录是D:\data\db，其中db文件夹也需要创建，岂不会自动生成

##### 启动mongodb

```D:\Program Files\MongoDB\Server\3.4\bin\mongod.exe --dbpath D:\data\db```

##### 但是这样启动太麻烦啦，我们不可能每次都这样来启动，所以我们把它配置成window服务：
在MongoDB的根目录创建 mongod.cfg 文件，内容：
```
systemLog:
    destination: file
    path: E:\MongoDB\data\log\mongod.log
    logAppend: true
storage:
    journal:
        enabled: true
    dbPath: E:\MongoDB\data\db
net:
    port: 27017
```

#### 安装服务
配置好上面后，在到cmd中执行（使用管理员权限）：
```D:\Program Files\MongoDB\Server\3.4\bin>mongod.exe --config "D:\data\config\mongod.cfg" --install```

##### 添加环境变量
在计算机->右键->高级->在环境变量PATH中加入"C:\Program Files\MongoDB\Server\3.4\bin"路径。

##### 启动服务
在cmd窗口中运行如下命令开始服务，也可以在可以在“控制面板\所有控制面板项\管理工具\服务”手动开启，注意默认是开机就自动启动服务的，可以设置成手动启动。

### 在nodeJS上应用
#### 1.链接到数据库
```
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/mydb";
 
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  console.log("数据库已创建!");
  db.close();
});
```

#### 2.链接到表
```
var MongoClient = require('mongodb').MongoClient;
var url = 'mongodb://localhost:27017/mydb';
MongoClient.connect(url, function (err, db) {
    if (err) throw err;
    console.log('数据库已创建');
    var dbase = db.db("mydb");
    dbase.createCollection('ybao', function (err, res) {
        if (err) throw err;
        console.log("创建集合!");
        db.close();
    });
});
```

#### 3.增
```
var MongoClient = require('mongodb').MongoClient;
var DB_CONN_STR = 'mongodb://localhost:27017/mydb'; 
 
var insertData = function(db, callback) {  
    //连接到表 site
    var collection = db.collection('site');
    //插入数据
    var data = [{"name":"源宝教程","url":"www.ybao.org"},{"name":"源宝工具","url":"code.ybao.org"}];
    collection.insert(data, function(err, result) { 
        if(err)
        {
            console.log('Error:'+ err);
            return;
        }     
        callback(result);
    });
}
 
MongoClient.connect(DB_CONN_STR, function(err, db) {
    console.log("连接成功！");
    insertData(db, function(result) {
        console.log(result);
        db.close();
    });
});
```

#### 4.删
```
var MongoClient = require('mongodb').MongoClient;
var DB_CONN_STR = 'mongodb://localhost:27017/mydb';    
 
var delData = function(db, callback) {  
  //连接到表  
  var collection = db.collection('site');
  //删除数据
  var whereStr = {"name":'源宝工具'};
  collection.remove(whereStr, function(err, result) {
    if(err)
    {
      console.log('Error:'+ err);
      return;
    }     
    callback(result);
  });
}
 
MongoClient.connect(DB_CONN_STR, function(err, db) {
  console.log("连接成功！");
  delData(db, function(result) {
    console.log(result);
    db.close();
  });
});
```

#### 5.查
```
var MongoClient = require('mongodb').MongoClient;
var DB_CONN_STR = 'mongodb://localhost:27017/mydb';    
 
var selectData = function(db, callback) {  
  //连接到表  
  var collection = db.collection('site');
  //查询数据
  var whereStr = {"name":'源宝教程'};
  collection.find(whereStr).toArray(function(err, result) {
    if(err)
    {
      console.log('Error:'+ err);
      return;
    }     
    callback(result);
  });
}
 
MongoClient.connect(DB_CONN_STR, function(err, db) {
  console.log("连接成功！");
  selectData(db, function(result) {
    console.log(result);
    db.close();
  });
});
```

#### 6.改
```
var MongoClient = require('mongodb').MongoClient;
var DB_CONN_STR = 'mongodb://localhost:27017/mydb';    
 
var updateData = function(db, callback) {  
    //连接到表  
    var collection = db.collection('site');
    //更新数据
    var whereStr = {"name":'更新数据'};
    var updateStr = {$set: { "url" : "https://www.ybao.org" }};
    collection.update(whereStr,updateStr, function(err, result) {
        if(err)
        {
            console.log('Error:'+ err);
            return;
        }     
        callback(result);
    });
}
 
MongoClient.connect(DB_CONN_STR, function(err, db) {
    console.log("连接成功！");
    updateData(db, function(result) {
        console.log(result);
        db.close();
    });
});
```