# OS_VM_env
关于虚拟机、Linux（主要是Ubuntu系列）下的环境安装等问题，并描述对应的解决方案

*然而实际中大家遇到的问题虽然报错信息一样，但是软件环境、各种配置参数、实际内容的微小差异等都可能导致需要不同的解决方法*  

*也因此，这里只能记录我自己在实际中解决方法，但会尽可能带上the circumstance of the problem，方便确认问题与解决方法是否合适*

## ubuntu换源
来源：https://cloud.baidu.com/article/3177211


> 什么是"源"？  
> 从网络上下载各种各样的软件，肯定会有一个文件的**来源**，能让你获取它的副本；  
> 这个来源肯定会有一个存储的设备，这个设备所在的物理位置，肯定是确定的；  
> 所以可以认为 换源 就是更换了你所选择的下载来源的设备（但是是以URL的形式表示
> 
> ...总的来说，"源"也就是软件来源...
### 方法(清华源的)
先看看这个地址:  
```
https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/
```
**操作步骤如下**：  

1. 备份  
```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
```

2. 更换文件内容  
用vim或者nano文件编辑指令，替换文件内容为上述给出的mirror地址里的东西

3. 更新源
```
sudo apt update
```

4. *更新软件
  
*此步为可选项，顺利完成第三步就已经成功了。更新OS上的软件也许会使运行更流畅？界面更好看？*
```
sudo apt update
```

---

## VMware 下Ubuntu 22.04.04虚拟机网络连接问题
### 问题描述
（VM网络连接为NAT模式，主机网络正常）
- 启动虚拟机后，右上角没有网络连接图案（只有电源图案和音量图案）
- ping www.baidu.com 失败， shell输出“ping: www.baidu.com: Temporary failure in name resolution”
- 打开浏览器firefox不能访问各种网站

### 解决方法
来源：https://blog.csdn.net/qq_48559526/article/details/134721151  

依次输入以下三条指令（第一条和第三条可能需要用户验证；第二条可能需要权限——加上sudo，是用来删除NetworkManager缓存文件用的）  
```
service NetworkManager stop
```
```
rm /var/lib/NetworkManager/NetworkManager.state
```
```
service NetworkManager start
```
至此我的网络问题已经解决了，CSDN blog里剩余的部分我并没有再执行

### 问题原因（推测）
主机的VPN不稳定，选择的节点不断变化导致的？

---

## VMware下，Ubuntu虚拟机添加（扩展）磁盘大小的方法
> 我一开始只觉得20GB的disk大小够用了（当然由于需要作为系统盘，系统也占用了不少）  
> 但是后来逐渐发现不太够用了怎么办？  
> 并且这个虚拟机上已经安装好的环境和数据，我懒得再在一个新虚拟机上重新安装/复制一遍，怎么办？  

诶，VMWare有扩展磁盘容量的功能————但是这个功能具体怎么用呢？添加了之后没有发现磁盘空间变大啊？  

可以使用gparted工具和gparted-live操作系统（这里应该是OS吧）  

### 解决方案来源
  
https://blog.csdn.net/Meteor_s/article/details/85068524  

### 具体操作步骤
（有点晚了今天，我先把要写的放在这，之后补上，有需要可以先看参考链接）  


#### 第0步 使用gparted工具管理磁盘分区
(这一步不是必须的)  
首先下载gparted工具:  
```
sudo apt-get install gparted
```
然后就可以使用gparted指令，对虚拟机的磁盘的不同分区(partition)进行操作了，不过你正在用的partition肯定是锁着的，操作不了(毕竟是你可能正在使用的磁盘)，所以要通过启动另一个操作系统来进行操作  

*事实上，这个操作系统gparted-live的界面长得跟gparted工具的基本一样*

#### 第1步 VMware虚拟机相关设置
(我使用的VMware版本是17 pro, 版本号17.5.2)  
- 首先我们需要下载gparted-live OS的映像文件(这里可以选与自己Linux操作系统类型相同的版本，比如你的ubuntu带有"amd64"，那你也下一个amd64的)
```
https://gparted.org/download.php
```
保存好这个.iso文件  

- 选择你想增大磁盘空间的虚拟机，编辑虚拟机——点击"编辑虚拟机设置"  
1. 选择"硬盘", 找到"扩展"按钮，点击它
2. 此时你会被要求填入扩展后的虚拟磁盘大小，比如你原来是20GB，想再增大20GB，那就填入40GB
3. 确认

#### 第2步 启动gparted-live操作系统
(我想应该可以认为gparted-live是一个专门管理磁盘的OS)  
那么如何启动它呢？  
- 我们需要通过进入BIOS, 通过Boot Menu来选择使用CD-ROM启动方式  

- 但，在此之前，先让我们更换操作系统：  
在虚拟机设置中，将CD/DVD(SATA)所使用的映像文件更换为我们第1步所下载的gparted-live-xxxxx.iso（这里要先记一下你所使用的原来的映像文件和路径）  
> 如果你有多个CD/DVD，就把他们使用的映像文件都换成gparted-live（反正我是这么做的，并且生效了; 否则可能还是启动Ubuntu"安装系统"）

- 进入BIOD Menu
在虚拟机启动的最初，会出现一个BIOS界面让你能够进入Boot Menu，但它出现的时间很短，你可能不好操作，并且不同VMWare进入Boot Menu的按键可能不一样(我猜的)

所以你可以找到虚拟机对应的vmx“配置文件”，在其中加入一行:  
```
bios.bootDelay = "10000"
```
这样你就有10秒的时间来看清该按什么键了  
(我是按的"Esc"键)  

然后选择有CD-ROM的那一行  

之后会出现一个gparted-live的安装界面，选择默认的“Gparted Live”，之后一直按回车(enter)就能启动gparted-live系统了


#### 第3步 操作分区，完成磁盘空间分配
这部分考虑到每个系统的partition位置有可能不一样，所以就抽象地描述一下，相信你能做到的！  

> 简单来说，就是把一块unallocated区域分配给你正在使用的磁盘空间(partition)  
> 需要的条件是：这个unallocated区域和你的主partition是连着的
>
> 而你可以进行的操作有：修改partition大小；划分一个空闲的partition哪部分是可分配的，哪部分需要干别的

我这个VMware版本下，扩展后会在partition3后面给一个新的partition4，而我要做的就是让partition4前半部分尽可能多的空间标记为unallocated，  
然后修改partition3的size时就可以把这部分unallocated也纳入partition3了，于是磁盘空间扩展就完成了

---

## Ubuntu24.04-desktop开机黑屏，虚拟机繁忙问题
打开虚拟机设置，在“硬件”-“显示器”-“3D图形”那，取消勾选 加速3D图形  

---

## VMware 17 pro安装虚拟机后，无法在主机与虚拟机之间复制粘贴的问题
(明明安装了VMware Tools，并且也勾选了对应的虚拟机设置:"启用拖放"和"启用复制粘贴"，为啥还是不行？我怀疑是默认安装的VMware Tools没有更新到Win11的支持吧)  
一条命令解决：  
```
sudo apt-get install open-vm-tools open-vm-tools-desktop
```
之后重启就行了。  
不过文件拖放似乎还是有一些问题。但是文件与文本内容的复制粘贴，双向都行了
