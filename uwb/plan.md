# UWB 项目plan

项目地址: /home/wmt/myProj/uwb  -- pj uwb 打开目录



## 专业名词(#zymc)
__CMSIS-DAP__ 	编程器；仿真器；下载器
__OpenOCD__ 	是一个开源的兼容stlink jlink工具的 调试程序

## 灵感 
6. 实验障碍物影响程度
	其它制造商的<<使用注意事项>><<安装注意事项>>
	1. 淘宝599卖家 [全迹科技](http://www.ubitraq.com/html/index.html)
	
	2. 南京卖家
	3. "华星智控UWB" 隧道定位  间隔150米-200米放置一个基站
		一般基站安装高度建议在3米以上，这样安装的目地是减少遮挡，保证标签和基站之间可以看得见，看得见通信效果就可以保证。
	4. [郑州联睿电子科技有限公司](http://www.locaris-tech.com/a/new/hyzx/2018/0711/148.html)

	目前美国海军已经开发了一种军用的UWB定位系统PAL(Precision Asset Location)，在L波段工作，瞬时带宽可以达到约400MHz。参考点使用高速隧道二级管检测器来进行UWB脉冲的边缘检测，从而可以实现在多径环境中找到第一个到达的脉冲信息，通过优化算法算出待测点坐标。待测点有一个短脉冲发射器，峰值输出功率约0.25W，数据包突发长度40bits，发送周期5s，发射器平均输出功率-79dB/MHz。这个功率比FCC规定的功率还要低38dB。该系统的试验已成功，它在大型集装箱货物环境下可以达到理想的定位精度，但是在小型货物定位时，精度不够理想，改进的PAL系统的商用化正在进行之中。此外，美国AetherWire公司已经开发出最先进的芯片Aether5和Driver2，它是基于COMS和UWB频谱开发的，具有体积小、功耗低、穿透力强，不易被察觉和定位精度高等特点，现已广泛用于消防、反恐等重大领域。

5. 完成linux stm32的一系列过程
	有文档在uwb 文摘下面 <<HowTo_ToolChain_STM32>>
- 安装工具
- 基本的项目
-  


4. 完成uwb项目的函数调用头系图
	(可以参看callgraph的笔记, 在本项目源码中＂cflow＂目录下有生存文件)
	A. 函数调用顺序,主函数调用了什么子函数
	B, 子函数被引用的次数

1. arm Power Management__
	百度搜索以上关键词，将为开发省电的设备准备
2. 用在Linux下__
　　（Linux下的开发配置参考）
 　　　[参考１](https://www.aliyun.com/jiaocheng/165663.html) (STM32 Development For Linux)
3. MSIS-DAP vs. ST-LINK 区别


## 资源参考
[LICENSE: Using ARM compiler in Linux with MDK flex license](http://www.keil.com/support/docs/3912.htm)
[CMSIS文档](C:\Keil_v5\ARM\Pack\ARM\CMSIS\4.2.0\CMSIS\Documentation)
[ST-LINK V2 使用说明](/home/wmt/桌面/文摘/文摘/项目/uwb)
[U盘版 ST-LINK V2 配套资料]()(有很多资料压缩在一个包中，怎么样才能快速方便的使用）
[《走近科学》 20181031 智慧之网](http://tv.cctv.com/2018/11/01/VIDEscfKUIBnxqvCBey4a44r181101.shtml)(14分20秒---虚拟电子围栏)
[Move embedded programming from Keil to Linux](https://electronics.stackexchange.com/questions/95183/move-embedded-programming-from-keil-to-linux)

	思路方案：
		呈现方式有：资料总览；资料定位；资料局部层次；资料分类; 资料文件打开后定位
		打开方式：在资料流览类链接点击，或输入资料序号ID


----------------------------------------------------------------------------
------------ 历史 ---------------------------
----------------------------------------------------------------------------

## keil开发主流程 
(实验taobao卖家源代码 评估板 型号：D-DWM-PG1.7V )
(...内容笔记移至工具学习笔记中st　keil.md )
百度网盘上含有MDK包

## 购买开发板
以上客户指定芯片[选型差别](http://)
1. STM32F407VG (客户指定)
[407VG开发板, 卖家１](https://item.taobao.com/item.htm?spm=a230r.1.14.20.92d82c59HrOgQQ&id=549182265786&ns=1&abbucket=10&wwlight=cntaobao%E6%B7%B1%E5%9C%B3%E7%81%BF%E6%99%96%E7%94%B5%E5%AD%90%E4%BC%81%E4%B8%9A%E5%BA%97-%7B549182265786%7D#detail)
[407VG开发板, 卖家2](https://item.taobao.com/item.htm?spm=a230r.1.14.34.92d82c59H3bHrg&id=41232053742&ns=1&abbucket=10#detail)
[开发板以太网芯片为DM9000CEP](https://baike.baidu.com/item/DM9000C/1537926)

但驱动DM9000是否与其它以态网控制器是否不同，带来重复工作．
以下以太网控制器芯片：
- DM9000 	(4.3元) --> 有开发板
- W5500		(9.28元)
- LAN9220	(4.5元)
- ENC28J60	(6.9元)
- DP83848C	(20元)

[POE部分](https://www.sohu.com/a/220915408_692064)只在硬件层面关心，故DM9000是可以的．

2. STM32F103VG (客户指定)
TaoBao所有的开发板型号: F103 (VCT6 / VET6 /  ZET6); 
[项目相关型号表]()(由ic的差别来选型号)
[103VE开发板，卖家] (https://detail.tmall.com/item.htm?spm=a230r.1.14.90.1bdb6bfeLSiHX6&id=550314682034&ns=1&abbucket=10)

## 实验最简代码MDK与linux编译
完成

----------------------------------------------------------------------------
## 购买账单
入１：2000	入２：2500  
- 余：4500 - 1400 - 1117.6 = 1982.4

_以下有的不含运费_
- 买１：４件套uwb评估板		小计:1400
- 买２：DWM1000 模块	150x2=300
- 买３：407VG+DM9000	158x2=315.80
- 买４：apobico 开发材料x2	184.00	
				小计：800.10 
- 买５：J-LINK JTAG v9 x2	265元
				小计：265



