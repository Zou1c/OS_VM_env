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
### 方法(清华的)
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

## VMware 下Ubuntu 22.04.04虚拟机网络连接问题
### 问题描述
（VM网络连接为NAT模式，主机网络正常）
- 启动虚拟机后，右上角没有网络连接图案（只有电源图案和音量图案）
- ping www.baidu.com失败， shell输出“ping: www.baidu.com: Temporary failure in name resolution”
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
