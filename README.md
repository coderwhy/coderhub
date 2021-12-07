# coderhub
A coderhub developed using node koa

## 说明文档

下载：

```shell
git clone https://github.com/coderwhy/coderhub.git
```

进入项目目录：

```shell
cd coderhub/
```

安装依赖：

```shell
npm install
```

在项目目录下新建文件名为 `.env` 的文件，在该文件中添加如下内容（注意配置 `MYSQL_PASSWORD` 的值）：

```
APP_HOST=http://localhost
APP_PORT=8000

MYSQL_HOST = localhost
MYSQL_PORT = 3306
MYSQL_DATABASE = coderhub
MYSQL_USER = root
MYSQL_PASSWORD = 你的MySQL数据库root用户密码
```

> 注：本地需要安装 `MySQL` 数据库。并且，你需要创建 `coderhub` 数据库：
>
> ```mysql
> CREATE DATABASE IF NOT EXISTS `coderhub`;
> ```
>
> 然后在 `coderhub` 数据库中创建以下数据表：
>
> ```mysql
> CREATE TABLE IF NOT EXISTS `user`(
> 	id INT PRIMARY KEY AUTO_INCREMENT,
> 	name VARCHAR(30) NOT NULL UNIQUE,
> 	password VARCHAR(50) NOT NULL,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
> 	avatar_url VARCHAR(200)
> );
> 
> CREATE TABLE IF NOT EXISTS `moment`(
> 	id INT PRIMARY KEY AUTO_INCREMENT,
> 	content VARCHAR(1000) NOT NULL,
> 	user_id INT NOT NULL,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
> 	FOREIGN KEY(user_id) REFERENCES user(id)
> );
> 
> CREATE TABLE IF NOT EXISTS `comment`(
> 	id INT PRIMARY KEY AUTO_INCREMENT,
> 	content VARCHAR(1000) NOT NULL,
> 	moment_id INT NOT NULL,
> 	user_id INT NOT NULL,
> 	comment_id INT DEFAULT NULL,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
> 	
> 	FOREIGN KEY(moment_id) REFERENCES moment(id) ON DELETE CASCADE ON UPDATE CASCADE,
> 	FOREIGN KEY(user_id) REFERENCES user(id) ON DELETE CASCADE ON UPDATE CASCADE,
> 	FOREIGN KEY(comment_id) REFERENCES comment(id) ON DELETE CASCADE ON UPDATE CASCADE
> );
> 
> CREATE TABLE IF NOT EXISTS `label`(
> 	id INT PRIMARY KEY AUTO_INCREMENT,
> 	name VARCHAR(10) NOT NULL UNIQUE,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
> );
> 
> CREATE TABLE IF NOT EXISTS `moment_label`(
> 	moment_id INT NOT NULL,
> 	label_id INT NOT NULL,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
> 	PRIMARY KEY(moment_id, label_id),
> 	FOREIGN KEY (moment_id) REFERENCES moment(id) ON DELETE CASCADE ON UPDATE CASCADE,
> 	FOREIGN KEY (label_id) REFERENCES label(id) ON DELETE CASCADE ON UPDATE CASCADE
> );
> 
> CREATE TABLE IF NOT EXISTS `avatar`(
> 	id INT PRIMARY KEY AUTO_INCREMENT,
> 	filename VARCHAR(255) NOT NULL UNIQUE,
> 	mimetype VARCHAR(30),
> 	size INT,
> 	user_id INT,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
> 	FOREIGN KEY (user_id) REFERENCES user(id) ON DELETE CASCADE ON UPDATE CASCADE
> );
> 
> 
> CREATE TABLE IF NOT EXISTS `file`(
> 	id INT PRIMARY KEY AUTO_INCREMENT,
> 	filename VARCHAR(100) NOT NULL UNIQUE,
> 	mimetype VARCHAR(30),
> 	size INT,
> 	moment_id INT,
> 	user_id INT,
> 	createAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
> 	updateAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
> 	FOREIGN KEY (user_id) REFERENCES user(id) ON DELETE CASCADE ON UPDATE CASCADE,
> 	FOREIGN KEY (moment_id) REFERENCES moment(id) ON DELETE CASCADE ON UPDATE CASCADE
> );
> ```

启动项目：

```shell
npm run start
```

项目启动成功后，你可以像下面这样通过 `POST` 方式调用 `localhost:8000/users` 接口注册一个用户：

![image-20211207225700874](https://user-images.githubusercontent.com/44868357/145115408-0900b821-f7f8-4f71-8fcd-316faa0637c2.png)

然后，你可以用该用户去发布动态了。但在此之前，你需要像下面这样先根据用户名和密码获取 `token`：

![image-20211208060727130](https://user-images.githubusercontent.com/44868357/145115494-c60faac5-6674-4ce0-9db7-c0a852def79c.png)

之后，将获取到的 `token` 值添加到请求头中：

![image-20211208061131111](https://user-images.githubusercontent.com/44868357/145115522-aa36102f-3b15-4280-a39b-929cb40a3d8f.png)

并在 `body` 中添加你想发布的动态内容：

![image-20211208061237596](https://user-images.githubusercontent.com/44868357/145115553-c6d86a89-393e-4eeb-8d65-365296077a8c.png)

动态发布成功后，你可以像下面这样通过 `localhost:8000/moment?offset=0&size=10` 接口查询当前用户发布过的动态（如果动态数超过 `10` 条，则只查询 `10` 条）：

![image-20211208061704626](https://user-images.githubusercontent.com/44868357/145115570-d71b3902-e943-43ae-b5fb-c356d38cfb5d.png)

