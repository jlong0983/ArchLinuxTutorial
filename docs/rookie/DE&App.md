# 桌面环境与常用应用
官方文档: [安装后的工作](https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))  
本文只介绍最基本的，能使系统真正意义上可用所需的组件  
相关视频链接： 2020ArchLinux安装桌面环境和常用软件 视频文字结合效果更好

#### 1.确保系统为最新
```bash
pacman -Syyu    #升级系统中全部包
```

#### 2.准备非root用户
添加用户，这里添加wheel用户组是为了能够使用sudo提权。比如新增加的用户叫wallen
```bash
useradd -m -g users -G wheel -s /bin/bash wallen
```
设置新用户wallen的密码
```bash
passwd wallen
```
设置wheel组的用户能用sudo获取root权限:
```bash
visudo
```
找到这样的一行,把前面的注释符号#去掉:    
```bash
#%wheel ALL=(ALL) ALL 
```
:wq保存并退出即可。

#### 3.安装KDE Plasma桌面环境
```bash
pacman -S plasma-meta
```
#### 4.安装greeter sddm  
```
pacman -S sddm
```
重启电脑，即可看到欢迎界面，输入新用户的密码即可登录桌面

#### 5.开启32位支持库与ArchLinuxCN支持库
进入桌面后，搜索terminal，可以找到konsole。它是KDE桌面环境默认的命令行终端。
```bash
sudo vim /etc/pacman.conf 
```
去掉[multilib]一节中两行的注释，来开启32位库支持。  
在文档结尾处加入下面的文字，来开启ArchLinuxCN源。
```bash
[archlinuxcn]    
SigLevel = Optional TrustAll    
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch  
```
上面服务器的地址是清华的，也可用下面中科大的
```bash
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```
最后:wq保存退出，刷新pacman数据库 
```bash
sudo pacman -Syyu
```

#### 6.安装基础功能包
<!-- 3：安装自动补全工具  待确认这个包是否在kde

    pacman -S bash-completion  

待确认yay是否可通过ArchLinuxCN安装
     -->
接下来我们安装一些基础功能包
```bash
sudo pacman -S ntfs-3g                          #识别NTFS格式的硬盘
sudo pacman -S adobe-source-han-serif-cn-fonts  #安装一个开源中文字体
sudo pacman -S firefox chromium                 #安装常用的火狐、谷歌浏览器
sudo pacman -S yay                              #yay命令可以让用户安装AUR中的软件
```
[AUR](https://aur.archlinux.org/)

#### 7.设置系统为中文
如果想要系统换为中文，需要重新设置locale

编辑 /etc/locale.gen，去掉zh_CN.UTF-8的注释符号（#）。

然后使用 locale-gen 生成 locale。

    locale-gen

编辑

    /etc/locale.conf

    echo 'LANG=zh_CN.UTF-8'  > /etc/locale.conf

#### 8.安装输入法
此处选用fcitx5的默认输入法。  
[Fcitx5](https://wiki.archlinux.org/index.php/Fcitx5)

#### 9.显卡驱动
* 英特尔核心显卡
```bash
sudo pacman -S xf86-video-intel #英特尔核显
```

* Nvidia显卡  
    * 若为台式机，拥有独立的显卡，直接安装如下两个包即可。
    ```bash
    sudo pacman -S nvidia nvidia-settings
    ```
    [台式机显卡官方文档](https://wiki.archlinux.org/index.php/NVIDIA)  

    * 若为笔记本，除上述两个包，推荐安装optimus-manager。可以在核心显卡和独立显卡间轻松切换。
    ```
    sudo yay -S optimus-manager
    ```
    [笔记本双显卡官方文档](https://wiki.archlinux.org/index.php/NVIDIA_Optimus)

* AMD显卡  
群主目前没有AMD显卡或者笔记本，无法提供最佳实践
```bash
sudo pacman -S xf86-video-amdgpu    #amd显卡
```