#夏灯明每二次充电项目计划：

## To Do List
39. 关于串口异常时，重新侦测串口影响到了别的串口的对策：
    (先改f407ve 把A测试的button1 power on后，short前，不要关机了,防止开机时间过短)
    3个station示例，都各自在运行；
    当有一个station串口发生异常时,
    告诉其它station实例，要配合一下进行自锁
    当故障station恢复时，告诉其它stations可以解锁了。

    1. 故障station 怎么发出通知
        见以下说明 
    2. 各个station 怎么自锁和解锁
        术语：station掉坑是指，stationp实例用ttyUSB?串口通讯时，ttyUSB?串口对像崩溃了。
        每个站有检测开始信号阶段／整个测试过程阶段
        [在等待开始信号阶段]，插入"异常配合"函数，是不是有station 串口通讯掉坑了)
        (是有别人掉坑，自锁，等待别人自救，自救了掉坑的station解锁)

        掉坑是在[测试阶段]发生的,　当读数超时后，先不要remove串口，
        要插入“异常通知”函数,让其它station自锁,等别人自锁了，再进行remove等自救操作
        自救完成再插入“异常解锁”函数，让其它station可以继续run....
    对策：station类中写３个函数，关于锁的用法，[见](https://blog.csdn.net/lianchenglian/article/details/78134896)
        恢复函数：
            锁上
            锁状态=false
            解锁
            等待条件.唤醒所有(QWaitCondition用法)
                QWaitCondition 用于多线程的同步，一个线程调用QWaitCondition::wait() 阻塞等待，直到另一个线程调用QWaitCondition::wake() 唤醒才继续往下执行。
        暂停函数：
            锁上
            锁状态=true
            解锁
        
38. 测试一，button1开机，看３；３没有成功，button退出
    问，button1,是被什么指令退出的（step?),
        可不可以在看３ok后退出
        目前，看３时就执行短路了，短路一定要退出button1吗？
        如果不退出，会造成什么结果？

    理想的解决办法：
        因为在changeRyStep(4)button1 on 后，
        一直会等Camera:testResOcr结果，不为0
        (其实在不超时的情况下，没有看到３它一直为０的),
        开始检测OCR,等0.1秒，ac-off, 再等0.1秒，button1_on
        再等0.1秒，执行短路（button1_off)
        对策１：执行短路测试的过程中，button1_on 一直
            (这要要试实物，会不会有意外)

        结策２：在station.cpp ocrInStep1函数中，changeRyStep(2)短路之前
            由等待0.1秒，延长到0.3秒，看效果。
            优先实施）

37. 为什么数据库数据查找线程没有更新界面
    更新界面的功能是由谁完成的
    mythread.cpp cout << maxSn 看是否有新的sn产生

36. 都是在设置ry时($B1 --v1 step1 ry)导致mcu disconnect
    
35. 3站优先权算法
    A
    B
    C
    
    C让B, B让A, 
    $A1(开机) $A3(off all charge) $A4(power on)  $A2(短路) 
    // 每次发指令时，A站先报到，
                    其它站正在工作，先放弃手头的工作，等待A完成，其它站再重头开始刚才的工作　　
                    in changeRyStat()function:
                    while(){
                        AcomIs = false;
                        if Acom
                            unlock 
                            if(dataInSpec)
                                AcomIs = true;
                                return 1;
                            else
                                return 0;
                    } 

            A want set rys -> Acom 
                while(!AcomIs);
                if(AcomIs) 
                    changeRyStat(num);

                    bool chkAcom(){ }
                    if (chkAcom)
                        cancelMyWork();     -> inStation  
                                                            Done = doMyWork (在while中检查Acom); > 都要设置自已的Adone/Bdone/Cdone(in mcumang)
                                                            if (Done = false && McuMan::pauseSta == get'stationCode') A/B/C
                                                                while (chkAdone); 
                                                                redoMyWork()  --> 保证redo的
                                                             ...... normal procee
                        

34. 有两个问题
        １，　没有其它站产品，某站不会触发测试
        ２，　其它站在测，刚测好的站又回被触发再测

33. 问题1.测试1（或其它测试）会有产品不到位（刚走过，或未到位）异常上压
    分析：下压肯定是收到了startSig=true的信号
            有以下两种可能，1, MCU 通过A#start发出
                                (只有在testing = 0,　并且开测信号为１时发出)
                        　　2, MCU 能过D211xx 顺带发出(其它站的ry控制的返回)

    假设性分析：A#toStart发出去后，A开测动作了, 才出现A_testing = 1,
                    会不会造成两次发出A#toStart 并且两次测。
                    （实际上没有出现，并且testing = 1 应该比串口还先完成）
               A_testing=0测试完毕状态（１，有可能已经完成，２，有可能还未就位）
               　   在这种情况，发生了什么它会开测：
                    １.发出了A#toStart. 即主要可能是start_ok(plc信号有感应)=1
                    2. 以上１没有发生，testing 某种情况下为１d211完成继电器时返回
                    　rysOk(含testing=0)
                        
33. 问题2.当3站只有一站，并且未收到V1#start信号，就一直停在那里
    综合以上两个问题，改造方案：
        １，先把A#toStart 命令缩短，让命令尽快被发送和接收
            (已改)
            MCU: V1#toStart -> #B  类似 #A #C (防止与ryDone抢占串口)
                 usbd_cdc_if.c CDC_Transmit_FS() 使用了CDC_lock;
            (已改)
            PC:  mcuthread 处理以上命令 line:46 toStart -> #
            (结果：为什么，如果手动模式下，“开始测试”　一直有效，不打起来也不会测, 是因为没有‘D211'触发)
        ２，不在用Done反馈形式加入各站setting信息。（D111xx　-> 111不要）
            MCU: 暂不改
            (未改)
            PC: mcuthread 只把75行开始的setStartSig注释掉
             (Result: after comment this, some station can not trigged! )
        3, 以上２条改后，若有不开测的现象，再在MCU部分定时发送开测信号#A #B #C
          但要保证串口发送时，没有回复RyDone也在用串口(同上用了CDC_Lock)
            (未改)
            MCU: 分别在a.c v1.c v2.c processScan_?()中。
            　　　A_testing == 0 启动一人定时器
                  A_testing = 1 内部检查定时器有无大于一定值，大了再发一次
                        "#A",　并再次重置定时器
                  定时器由$A1进行复位，表示step已经运行起来
    -----------
34. 问题3.开机3出现的3太快就保护了
        PC: 在station的stepTest执行$A2短路前,先了解ocrthread'3'是否识别成功，再短路
            目前，ocrthread是两处识别完了，才出结果，现在要求'3'处，出一个中间结果，
            (但是，目前本来就在ocrthread中'3'ok了，才会短路。正常'3'不ok不会出现'E') 

32. communication method change:
    1. 理想的状态是
        mcu先写入预定命定的组合
        PC只发A 1 (station A, step1)
        PC只发B 2 (station V1, step2)
        
        Mcu按照预定的组合组合调用rySet
        (mcu接到命令后到, 回复啥？)
       
       当pc发了A1后，进入电流测试状态，限定2秒后仍不OK,再发一次

31. 可能有问题的情况
    1. 在未发出#start:ok之前，mcu不能向PC发出A#STAR信号
        这个从电脑端控制,
        现象是，pc想接到mcu的understand, 结果被A#Start给顶没了，
        所以pc一直无尽的understand, 即使收到了start信号
            对策，在等待的过程中，收到了start信号，取消undersand的等待
        问题2, 表象，只要按了一次start, pc不动作，后续再按都不会动作，
        (把就是  mcu已经发了一次，但没测试，)
        但是电脑上会显示收到信号。不过不知道pc是否会当作收到信号，并且有
        没有进入step1的测试。
            对策，只要收到start就作处理，检测的某站信号是一直在检测的，
            当这个信号有时，只有

        当某个station测试完毕后，发出res_ok/ng后，立即停止一会检测A#toStart
        让PLC关闭开测信号。

    2. 本地capture的1.JPG，实时更新到ＧＵＩ中
        (每次每地存了1.jpg后就发信号更新)
        建立一个信号完成（指定１/2, 刷新/并闭）
        
    3. NG时要改变ＧＵＩ的外框颜色
    4. ocr要加上时间限定
    5. 确认mythread, 有没有启动更新ＵＩ的值
    6. 当数据库更改后，要告诉station重新加载一次数据库数据。

30. 改造后的项目执行方案
    各个station 启动后，各司其职
    A V1 V2 
        两个部分：
            １，测试过程
            ２，未测试中
    MCU
        如何处理
    
29. 加camera 计划
   整个应用不执行GUI.
   通过语句控制气缸上／下，控钮推送
        所以要把condition的语包提出来，单独使用
        1. 还是要找到所有的仪表
        2. 确定下mcu的static 静态类
        3. mcu 发送condition 让气缸下将，

28. qt 用cross-serial 偶尔错误　
    unix.cc 1080行，pthread_mutex_lock(&this->write_mutex); 参数错误

25. 所有的由mcu串口发出的指令

24. 程序改造的问题解决
// 检查所有的设备是否就绪
    //　告诉单片机一切就绪
    // 对应1,　单片机断电后，电脑就不会给它“一切就绪”的信号
    // 对策1,
    //  电脑重开 (重新发命令，让单片机reset)
    //  单片机重开 (收到第一个指令，返回让电脑重设的票求<电脑中断所有的工作，重新开始run>)
    // run的工作
    // 管理3 个线程(A / V1 /V2)
    // 每个线程都会用到一个控制站类: stationStart, stationOk, stationNg, stationStype


23. 用C++　取代clj 完成自动测试
    要实现的功能是：
        1. 测试的进程用QT中建的线程控制
        2, 以上线程直接操作串口库
        3, 其实把c++ main 中的测试函数（不行，还是把test封装成库)

22. serial 编译的问题
    make 会调用CMakefile
    git 提示：把两个git(catkin serial)项目都放在一个空间进行catkin-make操作
        (临时编译空间为：~/serial-ws)

有时候为了方便，需要在编译时绑定共享库的搜索路径，这只需要设定链接器ld的参数即可，参数名为：-rpath，后面跟逗号分隔的路径，如：-rpath=/usr/lib，gcc如下使用：
gcc -Wl,-rpath=/usr/lib，这样运行时，就不需要设定LD_LIBRARY_PATH环境变量了。

21. 程序重写的顺序顺充
    1. 串口类库的编译与c++去调用它
        捷径是：test先编译出来，
            再弄出linux动态库的用法
            (库项目放在哪？wmt) 

20. 死机的原因是
    clj向万用表发令时失败，那个进程崩溃了（对策加串口write函数前加了try)
    并且加了日志输出，测试机的只存位置为/usr/bin/clj-log.txt

19. clj 超时机制要改
    现在的超时机制是什么？
    改成新的机制：
        无论是pc/mcu端，都另设一个线程来计时，
       　当达到超时时，两端各自复位
    方法：
        MCU 发起start时，开始计时。
        clj 收到start时，开始计时。

        每时每刻执行，由统一线程监测，每个clj future线程，是否超时
            （每个clj future线程建立时，ticks值置０）
        当future的ticks > 大于指定值时，不管future运行状态把它消除。
            那么


18. 电流NG
    * 电流ng->再测电流ok -> 可以接着测试
    * 电流ok->再测电流ng　-> 只能发命令rys, 并让rys进入到了充电状态－>pc进入不了测充电电流状态（qt发现不了有新的数据库记录）
    * 有的ng又可以连续测

    原因可能性分析：java收不到rysOk回复，所以java没有进入读电流状态
        或者java收到了rysok回复，进行测试的超时系统故障，不能再次计超时。
        
    java 发出ryset指令开始计时吗？因为第二次测已经执行了
    
    check-step_A [step] -> step 是指一行 ryset   记录
    每一recode操作，都作t-count计时， 

    "初步想法"，　rySet > 给mcu > clj一直等待返回，如果mcu没有返回，就一直等了.

17. adc检测上限
    adc被测电压为4.5v,　才可以执行短路
    adc电压是多少？
    9*10/(33+33+33+10) =0.82
    4.5*10/(33+33+33+10) =0.45

16. 短路没有达的“E"的显示效果
    短路的ry动作是charge 0, input 0, button 1,short 1
     为了直观，要把input0, 之后，也就是button 1之前，停10s,　看电压是否下降。

    
15. 站位1问题
    NG 重测 --> 充电电流一直有，不开始取数据，比较
    (１，先找出原因，不行的话复位，并且qt不要重启)

    -------------------
    １.　表象
        1. 显示器
            显示PASS,(可能上个的测试品的结果)，没有什么测试中的状态    
        ２. MCU led灯
            看不见，因为没打开盖板
        3. 气缸已经就位

    ２.  分析
        pc早已经向ＭＣＵ发了准备好信号　
        MCU 得到了产品定位信号，向pc发送了请求，pc告诉mcu, step1要定位哪些ry
        ry已经动作ok, 并告诉pc,pc这时向万用表取电流.
    
        qt 界面的更新

14. qt 项目管理java jar 程序的启动与关闭
    jar程序的关闭
        串口启动失败时关闭（为了再启jar, 来打开串口）
        qt程序退出时,要退出jar程序，防止下次启动有影响
        (实验　jar 启动后的ps aux nam)

13. 问题改造　

(1)    不能开机原数据没有清除
    应该在一开始测开机时就清除。
    应该不能开机ＮＧ时清除相应数据

(2)   两个同时测, v2 马上报ng
    也影响了v1 不能开机ng
    

 (3)   线程累加，是不是future 没有cancel

 (4)   v1 4档起时ng, qt显示不能开机


 (5)   两个同时测:v1好品，　v2空
    此时：v2完成弹起
        v1一直按着(实际是它按错位置了，但奇怪的是 "测试２位" 显示测试ＯＫ，可能是上一个ＯＫ，没有检测到测试中)
        java log 显示：计时不定　测试Ｎｇ　已写入数据库 V1开机故障；并且已经向MCU发送了测试结果。但气缸不弹出，像是没有收到rysNg_V1
        (可能两个同时to_MCU 结果导致)
        ---------------------------
        (只要是to_MCU的，都要进行回应确认, 像rysDone回应一样，做一个resDone反馈；收到反馈后，才向mcU发出下一个结果)

        

(6)    先测Ｖ２空机，未完，再推入Ｖ３ＯＫ机，
       Ｖ２空机是已开机，２档０ｖＮＧ，<---- QT 显示
       Ｖ３ok机３档也显示０Ｖ　ng <---- QT 显示
        java log显示：线程１８　Ｖ２　开机一直在０ｖ
          a.      Ｖ１插入后，线程20,写入了开机ok,(因为它本来就是Ｏｋ　机)
                但此后空机的线程１８Ｖ２也出现了开机ＯＫ(4.318)，电压值同Ｖ１的一样(4.318)，难道共享变量出错了。(这两个4.318都写入了数据库)

          b.     接下来18线程Ｖ２一直是0V, 20线程没v1有再读取数据，只是显示了rysDone=0 writeLock=0;
                都超时ＮＧ，写入了ＮＧ－> database : v2 2档　0v fasle ; v1 3档　４.318 false(其实v1 3档是 4.2~ 5,它是ok的，说明它还没机会in-spec ).
         ---------------------------
          a. 每个测试线程一直在检测一个变量（电压表数据,from_V1, from_v2所产生）。v1和v2两个线程所取的值肯定是通过其它线程所来的全局变量，
            　两个变量是怎么干扰的，哦，由于开始写的函数公用，用了同样的变量名字。另一个线程的变量名atom型，本性程同名，会串吗？
       

12. 改造方向
    １. 备份旧项目
    ２. 把do-run 函数转变成事务，（要无副作用）
    3 . 把future　-> dosync 
    ４. 实验

11. 问题１：
    两个站同时向MCU发了rys设置条件，并且是两站两线程。同时读写同一个串口
    每次写，要等锁打开了，再进行，
    锁在得到

    用了.start .Thread mcu的串口数据都进不来，就超时:w


    v2 停了
        v1　推进去
    v2 接着测了
        v1 又停了
    v2 又测了

    两个同时空测，有一个会不停的测


    V1
    V2结果false
    V1　已经结果NG了（３档），还在则v1的结果,
    把V1装上去接着测，此时v2显示pass

    v2发生了ok,并告知了mcu.
    V1停在了开机状态, 屏莫最后并取了一个电压数据不再取了。
    (取电压的工作是由pc做的，为什么它不再向万用表V1port write呢?)
    (也许write了，但on-byte回调没有回复)
    (有时为什么会不停的write,并不停的取电压，此时完全没有超时的概念)

    要想v1接着测，要使v2接着检查下一台. 

10. 测试pc发指令让ＭＣＵ rySetMore()
    pc端得到C#rysDone "指令"
    实验方法：
        pc端repl 命令行：open (ttyUSB99)
            写上命令 write( "STA V2,ok 0,ng 0,jack 1,z 1,button1 1" )
        看pc端有没有（C#resDone)
        并且看v2站的rys有没有被执行
    目的是验证，能否在ＭＣＵ端执行各站的批量rys设置，
    并且各站设置完后，都有没有返回结果。
    问题是：pc端会不会有on-byte产生，应该不会。
        对策写个新的main函数，让它完成这个主要功能。

9. 流程中断
    MCU指令：B#1
      1
      测得V1值是
      ....
      平均值是:
      nil
      已经MCU....结果false
      MCU指令: B#1
      1
      。。。。。。不显示了(B站也死了)
     其它的站还正常工作

8. 目标，让每个工位都工作起来

7. sql
    insert into spec (station, test_order, item, min_v, max_v) values ('A', 1,  '充电',0.3,10);
    insert into spec (station, test_order, item, min_v, max_v) values ('A', 2, '短路',0,0.02);
    insert into spec (station, test_order, item, min_v, max_v) values ('V1',1, '3档',4.2,5.0);
    insert into spec (station, test_order, item, min_v, max_v) values ('V1',2, '4档',2.8,3.5);
    insert into spec (station, test_order, item, min_v, max_v) values ('V2',1, '2档',5.2,6.0);
    insert into spec (station, test_order, item, min_v, max_v) values ('V2',2, '1档',6.7,7.1);

    update spec set min_v=0, max_v=0.02 where sn = 1;

sn | test_order | station | item | min_v | max_v 
----+------------+---------+------+-------+-------
  3 |          1 | A       | 充电 |   0.3 |    10
  4 |          1 | V1      | 3档  |   4.2 |   5.0
  5 |          2 | V1      | 4档  |   2.8 |   3.5
  6 |          1 | V2      | 2档  |   5.2 |   6.0
  7 |          2 | V2      | 1档  |   6.7 |   7.1
  1 |          2 | A       | 短路 |     0 |  0.02
(6 行记录)


6. 所有的都一次性完成，OK.NG判定
        1. 从数据库中得到spec 
        2. 写入数据库测试结果
        
5. a.c 中处理测试结果ok/ng 情况
    分解成函数不用:q

4. 要完成的主要目的：
      1, mcu：返回OK后进入下一步
      2, clojue：要进行ok/ng 判定

      3, 防止串口拥堵, 每次要检验串口的状态, 
         串口超时策略
            向串口发送请求的有：
                每个万用表读数
        　发送一个请求，有回音了才能发下一个　 
                １，先解决不要取30次数据) 
                2, mcu 有各站位的全局接收变量,　各ok/ng判定的串口回传，while
                3. 
    

3. PC上的两个程序（UI.JAVA), 一个数据库怎么进行相互通
    数据库变更发起通知（优雅的解决方法）-- 暂轻
    不进行数据存储的要直接送给对方。(qt&jar互操作?) -- 重

2. 数据结构设计　
    1. 测试规格
        测试站　测试项目　最小规格　最大规格
        电流站  充电电流    0.8      1.2
        电流站  短路电流    0.8      1.2
        电压站1 开机电压
        电压站1 上调电压

    2. 测试结果
        测试站　测试项目　测得结果  判定结果　测试时间　
        电流站  充电电流    0.8      1.2
        
    3. 执行部件动作时间调整
        测试站　执行部件　延迟执行时间 


        
1. MCU 与PC　Meter 通讯规化 
	需要的信号
	MCU   PC 通知的一切就绪信号(不然MCU无视产品就位信号,但是就位信号要保留,一旦就绪，就位信号生效)
		PC 测试的结果信号(规则 站位：结果)
	PC    MCU发出的各工作站测试的各类信号	
		(规则：站位＃测试类别<如电流１，电流２>)
	Meter PC发出的测试指令
		(解析由ＭＣＵ发出的：站位＃测试类别＃及电流１, 向不同的万用表发取数指令,再判断，)
		

## Goal
1. 用MATLAB实现逻辑及功能
2. 用QT设计界面
3. 用SQLite做数据库管理
4. 用电脑/MCU执行万用表操作
5

## To Do List
16.  clj 问题
    a: 要求测试一个数据ok, 再测下一个
    v1: 只能测１次
    
15.
        MEASure:VOLTage:DC?
        Returns the DC voltage measurement on the first display.
        Parameter: [None] | [Range(<NRf> | MIN | MAX | DEF)]
        Example: MEAS:VOLT:DC?
        >+0.488E-4
        Returns the DC voltage measurement as 0.0488 mV.

14. 单片机告诉电脑，取电流值过程
        clj 各串口的协议是， 

13. mx 生存的文件尽量少改，别建一个函数
12. 完成reertos中的多任务功能:
	建立任务 (已在初始化时完成)
	1. default 任务干的事
		让A任务运行（暂时，将来有信号控制各任务的启停）
		建立工作站（rys = AStation() )--> 发送rys 到电流任务 --> 再发送电流任务执行信号.
			或者rys已经存在(无变更的第二次测试)－－> 直接发执行电流测试任务信号　
	2. 电流任务干的事
		接收电流工作站的rys
		接收电流开始测试的信号--> 如果rys存在，执行测试

	3. Ticks任务干的事 
		接收电流工作者的rys(由谁发待定), 将被执行扫描，ticks++;

11. 调试步骤
	Program received signal SIGINT, Interrupt.
	HAL_Init () at Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.c:192
	192       HAL_InitTick(TICK_INT_PRIORITY);

rogram received signal SIGINT, Interrupt.
0x080033fe in HAL_Delay (Delay=0) at Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.c:395
395       uint32_t tickstart = HAL_GetTick();



10. Relay类相关实验
	任务１：vDefaultTask　读取本地文件中的指令，启动电流测试任务
		根据指令把相应的任务Resume();
		Resume之前，先把所有RY实例化，放入一个集合中，传递给Task1
		step:
			1. 先把ry类定义一个自我扫描函数，printf它的依赖对象。
	任务2: vTask1 电流测试任务
		制作测试状态 3个RY实例


建立三个实例，每个实例都会有一个函数在每ms执行一次，用来检测其实例依赖的对象的状态的改变（这修细节另论）
实验目的一，创建两个实例，一个实例被一个启动任务初始化时，就改变其状态
所有的对象初始化是在什么时候执行，因为要改参数，所以初始化要在每次完成参数修改后，都要重新执行一次初始化
要有一个任务，创建实例，

1. 下载开发板的taobao资料，两个任务控制LED灯
	STM32F407VET6 (pdf 407ve 可看电路图)
2. 根据学习示例，制作ＩＯＣ
3. 让项目MAKE,通过
4. 设计夏灯明的测试动作框架, la arm (回家途中完成)
5. 确定买哪些硬件, (临行前下单)
6. 4项完成后，开始编程，同时完善la arm <<设计书>>
7. 来桂一周内完成硬件动作逻辑　  
8. 打印ＧＤＢ调试艺术 pdf gdb

0. 需求分析
	执行气缸的进退，并且有异常要返回错误，并不执行动作
	气缸的动作是由一个信号控制的，气缸能不能动作要由几个外部信号决定的。
	实现与测试止的，在外部几人不同环境下的条件，执行控制查看，控制结果。
	不同的感应器状态的改变，产生不同的事件。该事件会结合一定的状态完成动作（执行延时再测试一个数据/或做其它的动作）
	总结：事件-》会改变状态。 状态的改变触发一定的事件。
	现在的想法是，手动执行一个信号，让气缸动作，气缸的外部信号先前预设，为了自动实验(能否用systest有效的隔离实验数据），外部信号在一定条件下会变化。（该变化会导致另一个事件或状态发生）

1. 设计系统状态图
 (1) 一个气缸的动作模拟
     下压/抬起 
 (2)
 1, 先完成电压测试的系统状态图
		预想：
		测试流程：上电 - 等待产品就位信号 -  就位 - </A>控制气缸<a1>- 开始测电压 - 电压结果确认</A> - 下一个循环控制(A)
		<a1>气缸到位检测与行程控制: ------>  
			relayA 和 RelayB 的控制，受气缸感应器的控制
				逻辑：当RA动作时，检测
**step1** 完成气缸的状态逻辑及代码生成
		  到位信号（各个缸都有信号,故设成变量） 上/下/前/后（设定变量）	
**step2** 完成一个工作站的所有气缸动作逻辑
**step3** 电压读数逻辑

2. MCU仿真

## 灵感
6. 文字已经不好描述意图，想想怎么完成才能用图形描述系图
	首先用笔画草图，再是用软件。用软件画图的原因是，每次都能在图一副图中修改，图纸随时找到并打开。
	打开图文的命令时： tex arm 	
5. 怎么建模内部逻辑控制
	看点灯的教程
4. 关于时钟配置的作用，及怎么看懂时钟配置

3. 关于界面设计
	用QT 还是用AXURE RP吧，看下它们专业的demo
1. 项目总时间
	预定1月前完成框架
	(框架目的是为了变更可以灵活对应)
	框架设计原则：
		可能有的变化：
			１．
2. 项目分步计划与时间(做哪些，做到什么程度，后续可以方便的做哪些)
	设想
		买什么样的ARM板/Relay板(它们怎么组装)
		接口有几种

	设计
		GUI (QT 外观分块和布局)
		串口通讯(MCU <-> PC <-> Metter)(可否先用matlab实验，再生成Ｃ代码)
		数据库设计（方便字段扩展的办法）(matlab 数据库的工具箱)
		
	购买
		ARM板，继电板(根据设计规化，购买相关部件)
			
	万用表数据读取(在设计matlab仪器控制之后)
	
