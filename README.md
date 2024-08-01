# OS_VM_env
关于虚拟机、Linux（主要是Ubuntu系列）下的环境安装等问题，并描述对应的解决方案

### ubuntu换源
来源：https://cloud.baidu.com/article/3177211


> 什么是"源"？  
> 从网络上下载各种各样的软件，肯定会有一个文件的**来源**，能让你获取它的副本；  
> 这个来源肯定会有一个存储的设备，这个设备所在的物理位置，肯定是确定的；  
> 所以可以认为 换源 就是更换了你所选择的下载来源的设备（但是是以URL的形式表示
> 
> ...总的来说，"源"也就是软件来源...
#### 方法(清华的)
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
