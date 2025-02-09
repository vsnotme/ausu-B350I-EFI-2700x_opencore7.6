# B350I-EFI-opencore
![image](https://github.com/vsnotme/B350I-EFI-opencore/blob/main/Resources/%E6%88%AA%E5%B1%8F2021-12-23%20%E4%B8%8A%E5%8D%8812.08.44.png)
### 概览
  B350I EFI opencore7.6 Monterey
  | Hardware | Model |
  |----------|-------|
  | 电脑型号 | X64 兼容 台式电脑 |
  | 操作系统 |Windows 10 专业版 64位（Version 2009 / DirectX 12）|
  | 处理器 |AMD Ryzen 7 2700X Eight-Core 八核 |  
  | 主板 | 华硕 ROG STRIX B350-I GAMING（AMD PCI 标准主机 CPU 桥）|  
  | 显卡 | AMD Radeon RX Vega 64 ( 8 GB / AMD ) |  
  | 内存 | 16 GB ( 海盗船 DDR4 3200MHz )|  
  | 主硬盘 | 三星 SSD 960 EVO 250GB ( 250 GB / 固态硬盘 ) |  
  | 显示器 | 优派 VSC0F30 VA2261 ( 21.5 英寸  ) |  
  | 声卡 | 瑞昱  @ AMD High Definition Audio 控制器 | 
  | 网卡 | 英特尔 I211 Gigabit Network Connection / 华硕 |
  
 [详细配置](https://github.com/vsnotme/B350I-EFI-opencore/blob/main/%E8%AF%A6%E7%BB%86%E6%8A%A5%E8%A1%A8.txt)
 
 ##### 可用：
- 支持随航
- wifi，需要支持Wireless USB Adapter Clover的usb网卡（cf-811ac）之类
- 声音
##### 问题：
- ~~睡眠：当有usb插在蓝色的usb3.0上时，睡眠将崩溃。请插在红色的usb3.1上或者使用USBhub插在3.1上并将它内建。保持蓝色usb3.0为空的。~~
- 在Big Sur及monterey中发现处于AMD的系统已经解决了USB导致的睡眠问题。
- ~~但是在某些奇怪的地方会导致唤醒，比如开启显示器电源的时候。~~(探明实际上是显示器停止给键盘供电之后，键盘切换到蓝牙模式导致唤醒主机，使用它解决：https://github.com/odlp/bluesnooze）
 ### 要注意的点
-  #### 某些ssd硬盘会导致安装系统失败比如我的OCZ -VERTEX460A，解决办法是在一块机械硬盘上安装好系统用克隆工具克隆到ssd
-  #### 相信找到这里的小可爱肯定知道需要获取 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)生成新的 SMBIOS ID 吧
-  #### 安装旧版本系统会出现错误，解决办法在安装界面的终端更改时间：
   ```
   date 070512052018.03
   （date 月日时分年.秒）
   ```
 [终端修改时间](https://jingyan.baidu.com/article/d169e18614c996436611d83e.html)
 
  
 ## 碰到的一些雷区
 #### 算是自己对AMD黑苹果的一些总结，把遇到的问题捋一捋
 ### OC启动过程遇到的问题
 -  主板上没有above 4g decoding选项的，bootargs中添加npci=0x200
 -  别忘记勾选dummypowermanagement
 -  过分关注cfg lock的问题，kernel选项下两个带cfg的别打勾
 -  config文件胡乱套用，要更新oc的话用新版本oc configurator打开config.plist文件再保存一下即可
 -  efi文件过于复杂，必要的几个即可
### 引导过程遇到的问题
 -  识别不到系统盘，勾选了嵌入式apfs中的enablejumpstart依然没有反应，请添加apfs.efi（对于oc0.7以上）
 -  RTC only single RAM blank(128k)错误，主板上没有above 4g decoding选项的，bootargs中添加npci=0x200； 网上说的打开oc中rtcchecksum选项并没有作用
 -  config文件胡乱套用，要更新oc的话用新版本oc configurator打开config.plist文件再保存一下即可，关键驱动文件一个一个更新，千万不要闲麻烦，否则后续会更麻烦
 -  lilu1.5.6以上不能在我的catalina工作

### 完善过程遇到的问题
 -  windows使用usbtoolbox定制USB时，到了依次拔插usb步骤时记得也把插着鼠标键盘的usb拔下来插usb3.0
 -  ~~随航请使用macpro1，1 2017型号~~（升级为montery后无效）
 -  随航使用imac 19，1  i9 9900k机型
### 修复睡眠过程遇到的问题
 -  amd睡眠问题纠结了非常久，这方面ssdt之类的被困住了，但是最后的确依靠零零散散的信息琢磨出了禁用_SAT的方法，dortaniad [Sleep crashing on AMD](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/extended/post-issues.html#sleep-crashing-on-amd)提到，关键点是hackintool工具中pcie栏中，usb controller的IOReg名称。ssdt是天坑最后还是无效。
 关于这一点[这里](https://github.com/mikigal/ryzen-hackintosh#Sleep)有更多信息
 -  以为的ec控制器的问题，结果不是
 -  以为用了ryzen power manegement就行，结果不行
 -  以为有了原生电源管理就能解决，结果加载ssdt-plug解锁后问题依旧
 -  以为是nvram的锅于是虚拟nvram 无果
 -  以为是rtc问题，添加相应kext添加启动参数后无果
 -  定制了usb后禁用_SAT的ssdt与usb控制器发生冲突
 -  结果插入到usb3.1接口便可以睡眠了（因为查到的资料都显示插在2.0才能睡眠），阴差阳错成功了
 -  最后更新Monterey才完美解决
## 额外知识
 - log show --last 1d | grep "Wake reason" //一天内唤醒原因
 - control + C//终端终止当前进程
 - 开机苹果标志异常，过大或者变扁苹果，升级显卡gop固件，不管是新显卡还是老显卡
 - [黑苹果开机logo探讨
](https://coconut-era-514.notion.site/logo-2cfeee0538fd4c288be74e9d31a38b02?pvs=4)

[免责声明](https://github.com/vsnotme/B350I-EFI-opencore/blob/main/%E5%85%8D%E8%B4%A3%E5%A3%B0%E6%98%8E.txt)
