#### Ubuntu14.04 在 vmware中进入unity模式方法
1.执行以下代码

sudo apt-get update
sudo apt-get install gnome-session-fallback
cd ~
wget https://raw.githubusercontent.com/graychan/notes/master/vmware/tools/vmware-xdg-detect-de
1
2
3
4
此时执行ls命令可以看到vmware-xdg-detect-de文件，如下图所示。

2.点击 虚拟机->重新安装vmware tools

3.将VMwareTools-9.9.2-2496486.tar.gz复制到Downloads/目录下
选中VMwareTools-9.9.2-2496486.tar.gz鼠标右击选择复制，选择左边的Downloads 目录，选中VMwareTools-9.9.2-2496486.tar.gz文件，鼠标右键选择提取到这里进行解压。

4.覆盖文件vmware-xdg-detect-de
执行以下命令进行覆盖，注意：/home/smc/是我自己的宿主目录，你的Ubuntu应该不一样，所以此处应该根据你的环境更改，反正是找到Downloads目录即可。

cp vmware-xdg-detect-de /home/smc/Downloads/vmware-tools-distrib/lib/bin32/vmware-xdg-detect-de -f
1
5.重新安装vmtools
注意：/home/smc/是我自己的宿主目录，你的Ubuntu应该不一样，所以此处应该根据你的环境更改，反正是找到Downloads目录即可。

cd /home/smc/Downloads/vmware-tools-distrib/
./vmware-install.pl
1
2
一路回车确定。
6.注销当前用户选择Metacity



————————————————
版权声明：本文为CSDN博主「smcdef」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/smcdef/article/details/72578732

#### 全屏

Ctrl+alt+T：打开终端



输入命令：sudo apt install open-vm*

运行之后重启一下虚拟机就可以了

#### secureCRT The remote system refused the connection问题解决

问题：



Ubuntu系统必须开启ssh服务后，XP或者其他的主机才可以远程登陆到Ubuntu系统。

1，安装软件包，执行sudo apt-get install openssh-server

Ubuntu缺省安装了openssh-client，如果你的系统没有安装的话，再用apt-get install openssh-client安装上即可。

2，然后确认sshserver是否启动，执行ps -e |grep ssh

 

如果只有ssh-agent那ssh-server还没有启动，如果看到sshd那说明ssh-server已经启动了。如下：



3，ssh-server配置文件位于/ etc/ssh/sshd_config，可以cat查看。可以定义SSH的服务端口，默认端口是22，也可以改成其他端口。

4，然后重启SSH服务sudo /etc/init.d/ssh resart。

5，XP机上选用熟悉的远程登录工具，设置Ubuntu的IP地址、开放的用户名和密码、协议是SSH2、端口22，即可。




笔者用的是secureCRT ，未出现乱码情况。如果出现乱码，根据情况修改。
出现乱码的原因是：linux系统安装的时候是采用的中文安装。

解决方法：点击 options->session_options->appearance->font->中文字体->字符集->gb231->确定返回上一级->charset encoding->utf-8

刷新控制台即可



————————————————
版权声明：本文为CSDN博主「al_bat」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ccyours/article/details/41278213







####  Linux下root用户用vim编辑器打开文件进行编辑，提示该文件为readonly

 * 用sudo 打开
 sudo vim /etc/shadow
 * ![img](https://www.runoob.com/wp-content/uploads/2015/10/vi-vim-cheat-sheet-sch.gif)



#### secureCRT 

链接的时候的用户名是登录名（**待机界面显示的**）