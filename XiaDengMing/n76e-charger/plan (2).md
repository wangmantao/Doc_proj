# Battery Plan

## 报价准备
1. 确定万用表的选型
　　固纬 USB口
　　选定为[GDM-8341/8342](http://www.gwinstek.com.cn/cn/download/downloadfilelist.aspx?id=1320) (所有的资料及驱动）
　　8342的优点：温度测量；USB存储；可配GPIB
　　电流测试范围：（允许负值）DC 5A Range: +/- 5A max
2. 写好报价单
3. 是否可以买到16点的税票
4. 显示器参考（若不带触摸功能，可以用鼠标)
- [10.4寸显示器](https://detail.tmall.com/item.htm?spm=a220o.1000855.1998025129.5.53991abfQpIlU3&pvid=e16008a6-5a5e-46db-9aeb-bb1361f04ddc&pos=3&acm=03054.1003.1.2768562&id=542583508838&scm=1007.16862.95220.23864_0&utparam={%22x_hestia_source%22:%2223864%22,%22x_object_type%22:%22item%22,%22x_mt%22:0,%22x_src%22:%2223864%22,%22x_pos%22:3,%22x_pvid%22:%22e16008a6-5a5e-46db-9aeb-bb1361f04ddc%22,%22x_object_id%22:542583508838}&skuId=3261937381712)
- [15寸显示器](https://detail.tmall.com/item.htm?id=542548440268&ali_refid=a3_430583_1006:1104460672:N:%E5%B7%A5%E4%B8%9A%E6%98%BE%E7%A4%BA%E5%B1%8F:09d7535f87ce83f1e9c30b3560f1b493&ali_trackid=1_09d7535f87ce83f1e9c30b3560f1b493&spm=a230r.1.14.1&skuId=3319935576873)
------
- [另外一牌子](https://item.taobao.com/item.htm?id=546907313235&ali_refid=a3_420434_1006:1125524299:N:%E8%A7%A6%E6%91%B8%E5%B1%8F%E6%98%BE%E7%A4%BA%E5%99%A8:c2f33add9f53f5c88b8dd9673b82da91&ali_trackid=1_c2f33add9f53f5c88b8dd9673b82da91&spm=a230r.1.1957635.45)
5. 主机
[eip](https://item.jd.com/27278458274.html#crumb-wrap)
[占美](https://item.taobao.com/item.htm?id=19411013748&ali_refid=a3_420434_1006%3A1106051967%3AN%3A%E5%B5%8C%E5%85%A5%E5%BC%8F%E5%B7%A5%E6%8E%A7%E6%9C%BA%3A2e928eb66561eb2328e7c793c4f67dc5&ali_trackid=1_2e928eb66561eb2328e7c793c4f67dc5&spm=a230r.1.1957635.15&wwlight=cntaobaofenshengshu-%7B19411013748%7D)

4. 星期一发出报价单

　　[报价单](/home/wmt/桌面/文摘/笔记/项目/XiaDengMing/Battery/报价单)

## 开发准备

### 工业一体机
**需求**
- 串口 x 2
- USB x 4 (3个万用表，一个盘数据导出)
- 上电自动开机
- 掉电无损坏

### 万用表相关资料
- 远程控制 [User Manual P102](怎么打开ＰＤＦ的指定的页＿书签)
- 指令概览　(P107)

### USB串口的通讯
USB 配置 (USB 驱动必须安装)

__步骤__
- PC 连接 Type A, host
- DMM 连接 Rear panel Type B, slave
- 速度 1.1/2.0 (full speed/high
- speed)
- 可选波特率 9600, 19200, 38400,
- 57600, 115200
- 奇偶校验 None
- 硬件流控制 Off
- 数据比特 8
- 停止位 1

__步骤__
1. 用 USB 接线连接后面板的 B 类 USB 接口.
2. 按下 MENU.
3. 进入 I/O on level 1.
4. 进入 USB on level 2.
5. 设置合适的波特率.
6. 按下 Enter t 进入波特率设定.
7. 按下 EXIT 退出 USB 菜单. 
