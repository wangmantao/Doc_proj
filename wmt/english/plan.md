#English 项目的计划 ## 要完成的目的
## 相关文件
1. 有些音标文件放在cf-8 shares中

## 录感
1. English 要从定时启动，改成来人侦测启动
2. 网络连接失败要抛出exception -- ok
3. 另建一个项目(detect)，确认时间段，查看ＤＢxxID是否在线，在线运行指定任务
4. 再建一个库项目(util), 所有的自用工具函数放在里面．
5. 两个tick线程（时间段线程；rfid线程），时间线程要控制rfid线程的启动和暂停
6. 明天星期一，上午定好时间，等待王中信中午回来自动放电影给他看
(7. english这科的学习目的是什么，应用怎么实施)
8. 把rfid-seril做成一个类, 在temp/test/my-daemon/调用这个类
9. my-daemon 调用clj-serial(回顾util.user是怎么调用的)
	
## 录感实现 
9. my-daempn 调用clojur类
	util.user 是在rfid-detect中调用的．参看它的prject.clj core.clj
	把my-daemon移动到myProj中并起名字：
		wmt/clj-serial-daemon
	my-daemon 要用到clj-serial类要做的事情：
		1. :depdencies [cc.taily/serial "0.1.0"]
		2. :require [cc.taily.util.serial :as serial]
	my-daemon daemon开启状态，用psql查询数据库，新数据有无
	原本clj-serial运行main-做的动作，转给daemon
		手动lein run --- daemon项目正常
		sudo jsvc --- get_pidf: -1 in /var/run/my-daemon.pid
		因为文件有变更，要重新lein uberjar 生成jar文件新的:w
		--> 运行成功；数据库也ＯＫ
		
8. 制作rfid-serial类
	已经做了个类，包含一个脚本，编译并上传到本地的库
	参考clj-util类 upLocalRepoo.sh
	clj-serial (util-serial) 不需要用Aot. 被jsvc直接调用的才需要aot

7. 先把程序运行起来（english); 再设计完善的功能
	
6. 王中信的id号: 222671
	开启电脑，开启显示器，开启java的两个服务(rfid & detect-> 指定wzx王中信)
	java 服务的快速实现，设定好时间段，及运行的程序．

5. [并发编程通讯](https://github.com/puniverse/pulsar)
[官方文档](http://docs.paralleluniverse.co/pulsar/)
　　先用clojure 官方的并发/并行(看clojure joy)
　　时间段线程，每２秒检查一次，进入某个段后，去做这个段规定的事情
    rfid线程，需要一直查数据库，当有符合条件时，就不要再查了；
　　　当条件不满足，是接着查
	rfid线程的启动和关闭条件:
	启动：进入了时间段; 事情已经完成
	关闭：不在时间段，或在时间段，便RFID已经有效
   获得最近一次的某用户的最近一次的move_state
select min(move_state)  from locations where lable_id = 222671;
　　
4. 另建项目的[位置是：](~/myProj/wmt/util)
	所有自建空间.类，都在src/cc/taily/util/类(文件)名　依次展开
	ns cc.taily.util.users
	把english/clj-serial中的代码利用过来,目的是获得用户名的rfid
	   get-user-sn [name]; 
	先看数据库中的表我，和字段名，再看源代码中谁用到它了
	（流程：用用户名得到其rfid, 再用rfid得到其某种状态--用关联查询，或视图）
	english/users/lable_id字段 存放的是用户的rfid编号
	english/locations表　有move_state 和 move_time 存放的是rfid状态
	
        编译

	localRepo处理

	[detect]项目中试用
		:depdencies[cc.taily.util.xx]
		:require []
	defn get-rfid [name]
	

3. 另建项目的[位置是：](~/myProj/wmt/detect)
	函数流程：把一天预订三个时间段（早；中；晚）．每２秒钟检查系统时间，有没有进入某个时间段．进入段中后，每两秒钟向ＤＢ寻问本人的ＩＤ是否有in的动作
 如果有，打开该段指定的程序．
	设计一个２秒定时器对像，给这个定时器添加一个函数，每２秒都会运行这个函数；并且这个定时器可以被控制开启和关闭．
	


2. 在哪里执行的远程sql调用,clojure 怎么用catch + throw
(try
	(..)
	(catch Exception ex
		(.getMessage ex))) ;; --> ok
但要想个办法，在多个jdbc处理时避麻烦的try catch写法


1. English 要从定时启动，改成来人侦测启动
原业的项目检测串口数据的程序运行起来
再把它改成jar一直启动
这个程序，同时（暂时性）也处理打开要启动程序的逻辑
方法：
	按时间段侦测，先判定时间段，再判断有没有人与号出现，
人员（号）	时间段　 出现  任务
wzx		中午    1 	打开英语练习


