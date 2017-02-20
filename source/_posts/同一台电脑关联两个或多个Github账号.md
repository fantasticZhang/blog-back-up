---
title: 同一台电脑关联两个或多个Github账号
date: 2016-12-07 15:35:28
categories: github
---

今天遇到了一个问题，我自己有一个github账号，项目组有一个github账号，然后我需要在我的电脑上同时管理这两个github账号的仓库，这时候该怎么办呢？在查阅了一些资料后，成功解决了这个问题，现将解决方案记录下来，以便日后查看。

## 思路

管理两个SSH key。

## 解决方案

### 生成SSH key

`ssh-keygen -t rsa -C "注册邮箱"`

使用上面的命令在本地生成SSH key。由于需要管理两个github账号，因此在保存所生成的SSH key时需要注意两个key的文件名要有区别，比如我第一个账号生成的文件名为`id_rsa`，第二个账号生成的文件名为`id_rsa.gtensor`，这样就可以加以区分。

### 将SSH key添加到github

将上一步所生成的两个SSH key分别添加到对应的github账户中。

### 创建config文件

`$touch config`

在.ssh目录下，使用以上命令新建一个config文件，并在文件中添加如下内容：

	#default github
	Host github.com
	  HostName github.com
	  IdentityFile ~/.ssh/id_rsa	
	#gtensor github
	Host github_gtensor
	  HostName github.com  
	  IdentityFile ~/.ssh/id_rsa.gtensor
	
其中，Host后面就是github账号的别名，这里就是为第二个github账号新增了一个别名`github_gtensor`，这样就将不同的账号区分开了。config文件的内容，需要根据自己的实际情况进行相应修改。

### 将SSH key添加到ssh-agent上

`$ ssh-add ~/.ssh/id_rsa.gtensor`

在.ssh目录下，使用上面的命令将第二个账号的key添加到agent上。在执行以上命令时可能会出现错误提示： `Could not open a connection to your authentication agent`，此时需要执行以下命令开启ssh-agent服务：

`$ eval $(ssh-agent)`

### 测试github连接是否成功

`$ ssh –T git@github_gtensor`

使用以上命令测试是否能连接github账户，其中`github_gtensor`即为我所设置的第二个github账号的别名，需要根据实际情况进行修改。

### 使用

通过以上配置，我们就可以通过使用github.com的别名github_gtensor来明确说明我们要使用id_rsa.gtensor的SSH key来连接github，即使用项目组的github账号进行操作。

例如，要克隆第二个github账号的远程仓库到本地，则原来的写法：

	$ git clone git@github.com: 第二个github账号的用户名/***.git

则现在的写法改为：

	$ git clone git@github_gtensor: 第二个github账号的用户名/***.git
	
我的环境是：win10 + git bash，测试成功，如有任何疑问，可给我留言。