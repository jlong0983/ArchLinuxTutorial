# 桌面环境与常用应用

官方文档: [安装后的工作](<https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>)  
本文只介绍最基本的，能使系统真正意义上可用所需的组件  
相关视频链接： [2020ArchLinux 安装 KDE 桌面&Fcitx5 输入法配置](https://www.bilibili.com/video/BV1Vk4y117jc) 视频文字结合效果更好  
注: 文档中带有 <sup>AUR</sup> 角标的软件代表是用户自行打包的第三方软件[AUR](https://aur.archlinux.org/)，不在 Arch 官方支持范围内，可能会出现各种问题。如果不是实在没有官方支持的同类软件，则不建议使用。

#### 1.确保系统为最新

```bash
pacman -Syyu    #升级系统中全部包
```

#### 2.准备非 root 用户

添加用户，比如新增加的用户叫 wallen

```bash
useradd -m -G wheel -s /bin/bash wallen  #wheel附加组可sudo进行提权 -m同时创建用户家目录
```

设置新用户 wallen 的密码

```bash
passwd wallen
```

编辑 sudo 文件

```bash
EDITOR=vim visudo
```

找到这样的一行,把前面的注释符号#去掉，`:wq`保存并退出即可。

```bash
#%wheel ALL=(ALL) ALL
```

这里稍微解释一下
%wheel 代表是 wheel 组，百分号是前缀  
ALL= 代表在所有主机上都生效(如果把同样的`sudoers`文件下发到了多个主机上)  
(ALL) 代表可以成为任意目标用户  
ALL 代表可以执行任意命令  
一个更详细的例子:

```bash
%mailadmin   snow,rain=(root) /usr/sbin/postfix, /usr/sbin/postsuper, /usr/bin/doveadm
nobody       ALL=(root) NOPASSWD: /usr/sbin/rndc reload
```

组 mailadmin 可以作为 root 用户，执行一些邮件服务器控制命令。可以在 "snow" 和 "rain"这两台主机上执行  
用户 nobody 可以以 root 用户执行`rndc reload`命令。可以在所有主机上执行。同时可以不输入密码。(正常来说 sudo 都是要求输入调用方的密码的)

#### 3.安装 KDE Plasma 桌面环境

```bash
pacman -S plasma-meta konsole dolphin  #安装plasma-meta元软件包 直接回车回车
```

#### 4.配置 greeter sddm

<!-- pacman -S sddm //包含在plasma-meta了 不用单独装
但是plasma-desktop 没有 还是要装
-->

```
systemctl enable sddm
```

#### 5.开启 32 位支持库与 ArchLinuxCN 支持库

```bash
vim /etc/pacman.conf
```

去掉[multilib]一节中两行的注释，来开启 32 位库支持。  
在文档结尾处加入下面的文字，来开启 ArchLinuxCN 源。

```bash
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

上面服务器的地址是清华的，也可用下面中科大的

```bash
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

[此处](https://github.com/archlinuxcn/mirrorlist-repo#arch-linux-cn-community-repo-mirrors-list)为 archlinuxcn 全部仓库地址 可以根据自己实际情况另行选择。

最后:wq 保存退出，刷新 pacman 数据库

```bash
pacman -Syyu
```

重启电脑，即可看到欢迎界面，输入新用户的密码即可登录桌面

#### 6.安装基础功能包

进入桌面后，搜索 konsole。它是 KDE 桌面环境默认的命令行终端。  
接下来我们安装一些基础功能包

```bash
sudo systemctl disable iwd                                                  #确保iwd处于关闭状态，其无线连接会与NetworkManager冲突
sudo systemctl stop iwd                                                     #同上
sudo systemctl enable --now NetworkManager                                  #确保先启动NetworkManager，并进行网络连接 若iwd已经与NetworkManager冲突 则执行完上述步骤重启一下电脑即可。
sudo pacman -S ntfs-3g                                                      #识别NTFS格式的硬盘
sudo pacman -S adobe-source-han-serif-cn-fonts wqy-zenhei                   #安装几个开源中文字体
sudo pacman -S adobe-source-han-serif-kr-fonts noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra  #安装谷歌开源字体及表情
sudo pacman -S firefox chromium                                             #安装常用的火狐、谷歌浏览器
sudo pacman -S ark                                                          #与dolphin同用右键解压
sudo pacman -S gwenview                                                     #图片查看器
```

接下来要安装 archlinuxcn 源的相关步骤，

```bash
sudo pacman -S archlinuxcn-keyring                                          #cn源中的签名(archlinuxcn-keyring在archLinuxCn)
sudo pacman -S yay                                                          #yay命令可以让用户安装AUR中的软件(yay在archLinuxCn)
```

若安装 archlinuxcn-keyring 时报错，是由于密钥环的问题，可先按照[此链接](https://www.archlinuxcn.org/gnupg-2-1-and-the-pacman-keyring/)执行其中的命令，再安装 archlinuxcn-keyring

#### 6.5 检查家目录

检查家目录下的各个常见目录是否已经创建，若没有则需手动创建。

```bash
cd /home/wallen
mkdir Desktop Documents Downloads Music Pictures Videos
```

#### 7.设置系统为中文

打开 _System Settings_ > _Regional Settings_ > _Language_ 中选择中文加入，再拖拽到第一位，Apply 重新登陆即可。

<!-- optional KDE应该不需要了 在 kde 的区域设置里直接加上中文就行了
https://wiki.archlinux.org/index.php/Localization_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)/Simplified_Chinese_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E5%9F%BA%E6%9C%AC%E4%B8%AD%E6%96%87%E6%94%AF%E6%8C%81
如果想要系统换为中文，需要重新设置 locale

编辑 /etc/locale.gen，去掉 zh_CN.UTF-8 的注释符号（#）。

```bash
locale-gen  #重新生成locale
```

编辑 /etc/locale.conf

```bash
echo 'LANG=zh_CN.UTF-8'  >> /etc/locale.conf

```-->

#### 8.安装输入法

[Fcitx5 官方文档](<https://wiki.archlinux.org/index.php/Fcitx5_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>)  
中文及日文输入法均体验良好。

```bash
sudo pacman -S fcitx5-im #基础包组
sudo pacman -S fcitx5-chinese-addons #官方中文输入引擎
sudo pacman -S fcitx5-anthy #日文输入引擎
sudo pacman -S fcitx5-pinyin-moegirl #萌娘百科词库 二刺猿必备(ArchLinuxCn)
sudo pacman -S fcitx5-material-color #主题
```

设置环境变量 编辑文件 `vim ~/.pam_environment` 加入以下内容。konsole 以及 dophin 都需要这些环境变量，倒是 chrome 和 firefox 都不需要就可以输入中文

```bash
INPUT_METHOD DEFAULT=fcitx5
GTK_IM_MODULE DEFAULT=fcitx5
QT_IM_MODULE DEFAULT=fcitx5
XMODIFIERS DEFAULT=\@im=fcitx5
```

打开 _系统设置_ > _区域设置_ > _输入法_

点击`添加输入法`，找到简体中文下的 Pinyin ，点击添加

找到 _配置附加组件_
拉到下面找到 _Pinyin_ 点选`云拼音`和`在程序中显示预编辑文本` 最后应用  
找到 _classic user interface_ 在主题里选择一个你喜欢的颜色 最后应用

注销，重新登陆，就可以发现已经可以在各个软件中输入中文了

#### 9.启动蓝牙(若有)

```bash
sudo systemctl enable --now bluetooth
```

#### 10.字体设置

个人的设置是英文使用 Hack，中文使用 WenQuanYi Micro Hei。如果 telegram 字体遇到锯齿问题可以尝试 125%缩放，最新版本 KDE 貌似会同时改动字体 DPI 到 120。同时建议禁用'AR PL New Sung'等，和'WenQuanYi Zen Hei'这两个字体，因为 tg 貌似是自己有一套规则选择字体，会优先使用这两个字体，他们在 tg 里看着非常难受。

另外，telegram-desktop 这个包里 ttf-opensans 应该是强制依赖，不是可选依赖。没有安装的需要手动安装 ttf-opensans，否则英文字体会显示异常。
