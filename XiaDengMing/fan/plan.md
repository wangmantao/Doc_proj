#风量测试项目

## steps
    1. 添加库
    2. 测试测仪
    3. 制作测试界面
    4. 添加测试电流类库
    5. 

## 笔记
5. 识别两个usb0 1
    一个是万用表
    一个是relayBox
    算法：
       先确定有没有两个ttyUSB*
       有的话，
       先找万用表，
       另一个是relayBox

4. 控制器relay
    开关　十六进制指令
    情景 RTU 格式（16 进制发送） RTU 格式（16 进制接收）
查询 1-4 通道开关量 01 02 00 00 00 09
B8 0C
01 02 02 0xXX 0xXX CRC
CRC
控制第 1 路吸合 01 05 00 00 FF 00 8C 3A      01 05 00 00 FF 00 8C 3A
控制第 1 路断开 01 05 00 00 00 00 CD CA     01 05 00 00 00 00 CD CA
控制第 2 路吸合 01 05 00 01 FF 00 DD FA      01 05 00 01 FF 00 DD FA
控制第 2 路断开 01 05 00 01 00 00 9C 0A      01 05 00 01 00 00 9C 0A
控制第 3 路吸合 01 05 00 02 FF 00 2D FA     01 05 00 02 FF 00 2D FA
控制第 3 路断开 01 05 00 02 00 00 6C 0A     01 05 00 02 00 00 6C 0A
控制第 4 路吸合 01 05 00 03 FF 00 7C 3A     01 05 00 03 FF 00 7C 3A
控制第 4 路断开 01 05 00 03 00 00 3D CA      01 05 00 03 00 00 3D CA

全部吸合 01 0F 00 00 00 08 02 FF FF E5 30

读取输入点 X 状态：02
计算机向串口继电器控制板发送： 设备站号 命令 开始地址 需要读取数目 CRC 校验
串口继电器控制板 返回： 设备站号 命令 数据大小 有效数据 CRC 校验
读状态：X0-X7 X10-X13
发出 0x01 0x02 0x00 0x00 0x00 0x08 0x79 0xCC
接收 0x01 0x02 0x01 0xXX 0xXX 0xXX
 ↑ ↑ ↑
 有效数据 两字节 CRC 校验
 如下举例 0x32
 假设接收到的报文是：0x01 0x02 0x01 0x32 0x20 0x5D
 其中 0x32 代表了 X0-X7 的状态：0x32 对应的 8 位二进制代码是：0011 0010 ，最高位
表示 X7, 最低位表示 X0,
 是 1 表示有输入状态，这个数据表示：
X7 X6 X5 X4 X3 X2 X1 X0
0 0 1 1 0 0 1 0
X10-X13:（分析数据原理同上）
发出 0x01 0x02 0x00 0x0A 0x00 0x04 0x59 0xCB
接收 0x01 0x02 0x01 0xXX 0xXX 0xXX
备注：位为 1 代表有输入（接通电源）。位为 0 代表没有输入（没接通电源

3. 取实时数据的指令
    0xb3

2. fan test dev发送和接收的一些用法
    1. 实时采集命令0xb3
            发送一次命令，会有总共3*8=24bytes返回,前８个对我有用
            3次read才能把24bytes读完
    2. 程充自动读取一段时间后，de休眠了, read无反应了。
    


1. fan test device info.
Device Found	                        Device Found
type: 1234 5678 　  (pid uid 可以一样)	type: 17ef 6039 
path: /dev/hidraw3  (path会不一样)	    path: 0003:0002:00
serial_number:	                        serial_number: (null) 
Manufacturer: 	                        Manufacturer: (null)
Product:(null)	                        Product:(null)
Release:1 	                            Release:170 
Interface:0	                            Interface:0




## 
1. 规格参数
　　　pdf AVM07\_泰仕电子.pdf
2. toabao 有新的替代品
