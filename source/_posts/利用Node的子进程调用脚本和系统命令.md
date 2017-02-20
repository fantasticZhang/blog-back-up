---
title: 利用Node的子进程调用脚本和系统命令
date: 2016-12-04 14:48:56
categories: 前端
tags: Node
---

child_process模块使得Node可以随意创建子进程。它提供了4个方法用于创建子进程。

* spawn(): 启动一个子进程来执行命令。
* exec(): 启动一个子进程来执行命令，与spawn()不同的是其接口不同，它有一个回调函数获知子进程的状况。
* execFile(): 启动一个子进程来执行可执行文件。
* fork(): 与spawn()类似，不同点在于它创建Node的子进程只需要指定要执行的JavaScript文件模块即可。

## 方法简介
### spawn()

`child_process.spawn(command[, args][, options])`

spawn方法返回一个对象流，适合于输出大量数据然后需要读取的应用场合。

使用案例：

	var shellProcess = require('child_process');
	free = shellProcess.spawn('free', ['-m']);

	// 捕获标准输出并将其打印到控制台
	free.stdout.on('data', function (data) {
		console.log('standard output:\n' + data);
	});

	// 捕获标准错误输出并将其打印到控制台
	free.stderr.on('data', function (data) {
		console.log('standard error output:\n' + data);
	});

	// 注册子进程关闭事件
	free.on('exit', function (code) {
		console.log('child process exit ,exit:' + code);
	});

### exec()

`child_process.exec(command[, options][, callback])`

exec方法会启动一个子进程来执行系统命令，并缓冲产生的数据，当子进程完成后回调函数就会被调用，可带有：

* 当命令成功执行，缓冲的数据；
* 当命令执行失败，错误信息。

exec方法并没有args参数选项，这是因为它允许我们执行多个命令，当使用exec方法时，如果需要传输参数到命令行，则参数应该作为整个命令字符串的一部分。

使用案例：

	var shellProcess = require('child_process');
	shellProcess.exec('chmod -R a+rwx start.sh', function(err,stdout,stderr){  //设置start.sh脚本的权限
	       if(err) {
	          return console.log('error:'+stderr);
	        }
	   });

### execFile()

`child_process.execFile(file[, args][, options][, callback])`

当外部可执行文件存在时，该可执行文件将携带参数args被执行，当可执行文件退出时，回调函数被调用，回调函数带有子进程的标准输入输出，来自外部可执行文件的标准输出将被内部缓冲保存。

使用案例：

	var shellProcess = require('child_process');
	shellProcess.execFile('./stop.sh', function(err, stdout, stderr){
	     if(err) return console.error(err);
	     console.log('stdout: '+stdout);
	});

### fork()

`fork(modulePath[,args][,options])`

其中modulePath是一个字符串，用来指定JavaScript文件路径。

由于该方法还没有实际使用过，所以目前对它还不太了解，在日后充分了解后再进行补充。

## 区别

* spawn()与exec()、execFile()的区别：

	* exec()、execFile()都有一个回调函数获知子进程的状况；
	* exec()、execFile()创建时可指定timeout属性设置超时时间，一旦创建的进程运行超过设定的时间将会被杀死。

* exec()与execFile()的区别：exec()适合执行已有的命令，execFile()适合执行文件。

## 补充
### 使用exec()方法执行sudo命令
由于sudo命令需要输入密码，所以执行sudo命令与执行普通命令有所区别，下面是两种比较简单的解决方法：

##### 方法一： -S选项

sudo命令有个-S选项，用于在需要输入密码的时候，读取密码。

假设密码为password，且假设要使用sudo命令执行脚本文件 tee_to_monitor.sh ，并向其传递参数 status ，那么，完整命令如下

`echo "password" | sudo -S ./tee_to_monitor.sh status`

相应的，node代码可以这样

	var shellProcess = require('child_process');
	shellProcess.exec('echo "zjl" | sudo -S ./tee_to_monitor.sh status',function(err, stdout, stderr){
		if(err) console.log('error: '+stderr);
		console.log(stdout);
	});

##### 方法二

该方法比较简单，只需要使用`sudo npm start`启动应用即可，假设同样是上面的例子，此时node代码如下：

	var shellProcess = require('child_process');
	shellProcess.exec('./tee_to_monitor.sh status',function(err, stdout, stderr){
		if(err) console.log('error: '+stderr);
		console.log(stdout);
	});

也就是说，如果使用sudo命令启动应用，则该应用中就不需要再用sudo了。

### 使用exec()方法实时获取输出

如果按照前面例子中的方法使用exec()，则子进程完成后回调函数才会被调用，如果想要实时获取输出，则可按照下面的例子使用exec()。

	var shellProcess = require('child_process');
	var child = shellProcess.exec('./start.sh');
	// 捕获标准输出并使用socket通信传递数据
	child.stdout.on('data',function(data){
	  app.io.sockets.emit('news', data);
	});
	// 捕获标准错误输出并使用socket通信传递数据
	child.stderr.on('data',function(data){
	  app.io.sockets.emit('news', data);
	});
	// 注册子进程关闭事件
	child.on('close',function(code){
	  console.log('closing code: '+code);
	});
