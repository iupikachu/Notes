## Linux学习







### 目录

* /bin       存放最常使用的命令

* /boot    linux启动时的核心文件，包括连接文件和镜像文件 （不要动）

* /dev      存放外部设备

* <font style="color:red">/etc      存放所有的系统管理所需要的**配置文件**和子目录</font>      

* /home  用户的主目录 每个用户都有自己的一个目录

* /lib        系统最基本的动态连接共享库 类似windows的DLL文件

* /lost+found   一般情况是空的，当系统非法关机时，存放一些文件

* /media  存放linux自动识别的设备挂载的目录

* /mnt      用户临时挂载别的文件系统 （可以将一些本地文件挂载在这个目录下）

* ==/opt       额外安装软件的目录 比如安装一个ORACLR的数据库  默认为空==

* /proc     虚拟目录  系统内存映射 可以直接访问这个目录来获取系统信息

* /root      系统管理员

* /sbin    Super User 存放系统管理员使用的系统管理程序

* /srv       存放服务启动后需要提取的数据

* /sys       linux2.6内核的大变化 安装了2.6内核中新出现的文件系统sysyfs

* ==/tmp     存放临时文件==  用完就丢的文件 比如 安装包

* ==/usr      非常重要的目录 用户的很多应用程序和文件都放在这个目录下 类似于windows的program files目录==

* /usr/bin  系统用户使用的应用程序

* /usr/src   内核源代码

* ==/var          存放不断扩充的东西 习惯将那些经常被修改的目录放在这个目录下 包括各种日志文件==

* /run        临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删除或清除

* ==/www      存放服务器网站相关的资源，环境，网站的项目==

  



### 常用的基本命令

>绝对路径：c:\Program\data\xxx.xxx  

> 相对路径:    比如说在data目录下 xxx.xxx 对应我们的相对路径就是  /xxx.xx

* cd   切换目录
  * ​	绝对路径 /
  * ​    相对目录  ../../

* ./     当前目录
* cd..  返回上一级目录
* ls     列出目录
  * -a：查看全部文件（包括隐藏文件）
  * -l： 列出所有文件包含文件属性与权限 没有隐藏文件

所有参数可以组合使用

* pwd   显示当前用户所在的目录

* mkdir  创建目录

  * rmdir  仅能删除空的目录
  * mkdir -p 递归创建层级目录
  * rmdir -p 递归删除层级目录

  

* cp  原来的地方  新的地方   复制文件

* rm  移除文件或者目录

  * -f     忽略不存在的文件 不会出现警告 强制删除

  * -r     递归删除目录

  * -i      互动，询问是否删除

    > rm -rf       删除系统所有的文件     **删库跑路**
    >
    > <img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022085832641.png" alt="image-20201022085832641" style="zoom:50%;" />

* mv   移动文件或者目录   重命名文件
  
  * -f  强制移动

> <img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022090217169.png" alt="image-20201022090217169" style="zoom:50%;" />

#### 文件属性

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022090859672.png" alt="image-20201022090859672" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022091035900.png" alt="image-20201022091035900" style="zoom:50%;" />



* chgrp  更改文件属组

  > chgrp [-R]  属组名  文件名     chgrp -R  root www
  >
  >  递归更改文件属组   更改某个目录文件的属组时，加上-R 那么该目录下的所有文件属组都会更改

* chown   更改文件属主 也可以同时更改文件属组     

  > chown -R root www

  * chown  [-R]   属主名 属组名

  * chown  [-R]   属主名 属组名 文件名

    

* ==chmod==    更改文件9个属性

> chmod [-R] xyz
>
> linux 文件三种身份   owner/group/others    各有自己的 read/write/execute
>
> [rwxrwxrwx]
>
> 数字表示：r:4   w:2  x:1
>
> 每种身份各自的权限是累加的  例如[rwxrwx---]
>
> * owner=rwx=4+2+1=7
>
> * group=rwx=4+2+1=7
>
> * others= --- = 0+0+0 = 0
>
>   chmod 770 filename



#### 文件内容查看

linux系统中使用以下命令查看文件内容

* cat   第一行开始显示文件内容

* tac   最后一行开始显示

  <img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022101359767.png" alt="image-20201022101359767" style="zoom:50%;" />

* nl    显示时，附带行号

  <img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022101503244.png" alt="image-20201022101503244" style="zoom:50%;" />

* more   一页一页显示文件内容     (空格代表翻页  回车代表向下看一行)

* less     与more类似 可以往前翻页  （空格翻页 上下键翻动页面 退出q    /向下查找字符串  /set 查找set字符串并标亮     ？向上查找字符串  n下一个  N上一个）

  <img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022102629543.png" alt="image-20201022102629543" style="zoom:50%;" />

* head   -n行数   只看头几行

* tail       -n行数  只看尾巴几行

  >可以使用man命令查看命令的使用文档   如  man cp



网络配置目录：/etc/sysconfig/network-scripts

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022100359862.png" alt="image-20201022100359862" style="zoom:50%;" />



查看当前网络情况

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022101205515.png" alt="image-20201022101205515" style="zoom:50%;" />





### vim编辑器



没安装vim？？



安装命令：

rpm -qa | grep vim //查看vim命令在什么软件包47.

出现 vim-minimal-7.4.160-4.el7.x86_64

yum install -y vim* //安装vim相关的软件包

rpm -qa | grep vim



![image-20201022134337195](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022134337195.png)





> Vim 按键说明

除了上面简易范例的 i, Esc, :wq 之外，其实 vim 还有非常多的按键可以使用。

**第一部分：一般模式可用的光标移动、复制粘贴、搜索替换等**

| 移动光标的方法     |                                                              |
| :----------------- | ------------------------------------------------------------ |
| h 或 向左箭头键(←) | 光标向左移动一个字符                                         |
| j 或 向下箭头键(↓) | 光标向下移动一个字符                                         |
| k 或 向上箭头键(↑) | 光标向上移动一个字符                                         |
| l 或 向右箭头键(→) | 光标向右移动一个字符                                         |
| [Ctrl] + [f]       | 屏幕『向下』移动一页，相当于 [Page Down]按键 (常用)          |
| [Ctrl] + [b]       | 屏幕『向上』移动一页，相当于 [Page Up] 按键 (常用)           |
| [Ctrl] + [d]       | 屏幕『向下』移动半页                                         |
| [Ctrl] + [u]       | 屏幕『向上』移动半页                                         |
| +                  | 光标移动到非空格符的下一行                                   |
| -                  | 光标移动到非空格符的上一行                                   |
| 数字< space>       | 那个 n 表示『数字』，例如 20 。按下数字后再按空格键，光标会向右移动这一行的 n 个字符。 |
| 0 或功能键[Home]   | 这是数字『 0 』：移动到这一行的最前面字符处 (常用)           |
| $ 或功能键[End]    | 移动到这一行的最后面字符处(常用)                             |
| H                  | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
| M                  | 光标移动到这个屏幕的中央那一行的第一个字符                   |
| L                  | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
| G                  | 移动到这个档案的最后一行(常用)                               |
| nG                 | n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu) |
| gg                 | 移动到这个档案的第一行，相当于 1G 啊！(常用)                 |
| n< Enter>          | n 为数字。光标向下移动 n 行(常用)                            |

| 搜索替换 |                                                              |
| :------- | ------------------------------------------------------------ |
| /word    | 向光标之下寻找一个名称为 word 的字符串。例如要在档案内搜寻 vbird 这个字符串，就输入 /vbird 即可！(常用) |
| ?word    | 向光标之上寻找一个字符串名称为 word 的字符串。               |
| n        | 这个 n 是英文按键。代表重复前一个搜寻的动作。举例来说， 如果刚刚我们执行 /vbird 去向下搜寻 vbird 这个字符串，则按下 n 后，会向下继续搜寻下一个名称为 vbird 的字符串。如果是执行 ?vbird 的话，那么按下 n 则会向上继续搜寻名称为 vbird 的字符串！ |
| N        | 这个 N 是英文按键。与 n 刚好相反，为『反向』进行前一个搜寻动作。例如 /vbird 后，按下 N 则表示『向上』搜寻 vbird 。 |

| 删除、复制与粘贴 |                                                              |
| :--------------- | ------------------------------------------------------------ |
| x, X             | 在一行字当中，x 为向后删除一个字符 (相当于 [del] 按键)， X 为向前删除一个字符(相当于 [backspace] 亦即是退格键) (常用) |
| nx               | n 为数字，连续向后删除 n 个字符。举例来说，我要连续删除 10 个字符， 『10x』。 |
| dd               | 删除游标所在的那一整行(常用)                                 |
| ndd              | n 为数字。删除光标所在的向下 n 行，例如 20dd 则是删除 20 行 (常用) |
| d1G              | 删除光标所在到第一行的所有数据                               |
| dG               | 删除光标所在到最后一行的所有数据                             |
| d$               | 删除游标所在处，到该行的最后一个字符                         |
| d0               | 那个是数字的 0 ，删除游标所在处，到该行的最前面一个字符      |
| yy               | 复制游标所在的那一行(常用)                                   |
| nyy              | n 为数字。复制光标所在的向下 n 行，例如 20yy 则是复制 20 行(常用) |
| y1G              | 复制游标所在行到第一行的所有数据                             |
| yG               | 复制游标所在行到最后一行的所有数据                           |
| y0               | 复制光标所在的那个字符到该行行首的所有数据                   |
| y$               | 复制光标所在的那个字符到该行行尾的所有数据                   |
| p, P             | p 为将已复制的数据在光标下一行贴上，P 则为贴在游标上一行！举例来说，我目前光标在第 20 行，且已经复制了 10 行数据。则按下 p 后， 那 10 行数据会贴在原本的 20 行之后，亦即由 21 行开始贴。但如果是按下 P 呢？那么原本的第 20 行会被推到变成 30 行。(常用) |
| J                | 将光标所在行与下一行的数据结合成同一行                       |
| c                | 重复删除多个数据，例如向下删除 10 行，[ 10cj ]               |
| u                | 复原前一个动作。(常用)                                       |
| [Ctrl]+r         | 重做上一个动作。(常用)                                       |

**第二部分：一般模式切换到编辑模式的可用的按钮说明**

| 进入输入或取代的编辑模式 |                                                              |
| :----------------------- | ------------------------------------------------------------ |
| i, I                     | 进入输入模式(Insert mode)：i 为『从目前光标所在处输入』， I 为『在目前所在行的第一个非空格符处开始输入』。(常用) |
| a, A                     | 进入输入模式(Insert mode)：a 为『从目前光标所在的下一个字符处开始输入』， A 为『从光标所在行的最后一个字符处开始输入』。(常用) |
| o, O                     | 进入输入模式(Insert mode)：这是英文字母 o 的大小写。o 为『在目前光标所在的下一行处输入新的一行』；O 为在目前光标所在处的上一行输入新的一行！(常用) |
| r, R                     | 进入取代模式(Replace mode)：r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下 ESC 为止；(常用) |
| [Esc]                    | 退出编辑模式，回到一般模式中(常用)                           |

**第三部分：一般模式切换到指令行模式的可用的按钮说明**

| 指令行的储存、离开等指令                                     |                                                              |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| :w                                                           | 将编辑的数据写入硬盘档案中(常用)                             |
| :w!                                                          | 若文件属性为『只读』时，强制写入该档案。不过，到底能不能写入， 还是跟你对该档案的档案权限有关啊！ |
| :q                                                           | 离开 vi (常用)                                               |
| :q!                                                          | 若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。    |
| 注意一下啊，那个惊叹号 (!) 在 vi 当中，常常具有『强制』的意思～ |                                                              |
| :wq                                                          | 储存后离开，若为 :wq! 则为强制储存后离开 (常用)              |
| ZZ                                                           | 这是大写的 Z 喔！若档案没有更动，则不储存离开，若档案已经被更动过，则储存后离开！ |
| :w [filename]                                                | 将编辑的数据储存成另一个档案（类似另存新档）                 |
| :r [filename]                                                | 在编辑的数据中，读入另一个档案的数据。亦即将 『filename』 这个档案内容加到游标所在行后面 |
| :n1,n2 w [filename]                                          | 将 n1 到 n2 的内容储存成 filename 这个档案。                 |
| :! command                                                   | 暂时离开 vi 到指令行模式下执行 command 的显示结果！例如 『:! ls /home』即可在 vi 当中看 /home 底下以 ls 输出的档案信息！ |
| :set nu    (设置行号)                                        | 显示行号，设定之后，会在每一行的前缀显示该行的行号           |
| :set nonu                                                    | 与 set nu 相反，为取消行号！                                 |



### 账号管理

在真实开发环境中 一般都不会用root账号

linux是一个多用户多任务的分时操作系统



> useradd 命令 添加用户 

useradd -选项  用户名

-m： 自动创建这个用户的主目录 /home/cqp

-G：  给用户分配组

参数说明：

- 选项 :

- - -c comment 指定一段注释性描述。
  - -d 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。
  - -g 用户组 指定用户所属的用户组。
  - -G 用户组，用户组 指定用户所属的附加组。
  - -m　使用者目录如不存在则自动建立。
  - -s Shell文件 指定用户的登录Shell。
  - -u 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。

- 







<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022155210725.png" alt="image-20201022155210725" style="zoom:50%;" />

linux中一切皆文件 创建用户时就是向文件写入信息   /etc/passwd 中最后一行就写入了cqp用户的信息

```bash
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:995::/var/lib/chrony:/sbin/nologin
vagrant:x:1000:1000:vagrant:/home/vagrant:/bin/bash
vboxadd:x:997:1::/var/run/vboxadd:/bin/false
cqp:x:1001:1001::/home/cqp:/bin/bash

```



> 删除用户 userdel

userdel -r cqp  删除用户时将他的目录页也一并删除



> 修改用户  usermod

对应修改的内容 修改那个用户

参数和useradd一样

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022161055223.png" alt="image-20201022161055223" style="zoom:50%;" />

usermod -d /home/970823  cqp  修改cqp用户的主目录

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022161113336.png" alt="image-20201022161113336" style="zoom:50%;" />

> Linux下如何切换用户

1.切换用户的命令为：su username 【username是你的用户名哦】

2.从普通用户切换到root用户，还可以使用命令：sudo su

3.在终端输入exit或logout或使用快捷方式ctrl+d，可以退回到原来用户，其实ctrl+d也是执行的exit命令

4.在切换用户时，如果想在切换用户之后使用新用户的工作环境，可以在su和username之间加-，例如：【su - root】

$表示普通用户

\#表示超级用户，也就是root用户





> 锁定账户

root 权限 锁定账号 这样这个账号就登陆不上了

``` bash
passwd -l cqp   锁住账号
passwd -d cqp   将密码清空 也不能登陆了
```







### 用户组管理

属主   属组

每一个用户都有一个用户组，系统可以对一个用户组（开发，测试，运维，root）中的所有用户进行集中管理。

对用户组的管理：增加 删除 修改  实际上就是对/etc/group文件的更新



> 创建一个用户组  groupadd

  -g 可以指定id   如果不指定就自增1

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022182853969.png" alt="image-20201022182853969" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022182904633.png" alt="image-20201022182904633" style="zoom:50%;" />



> 删除用户组  groupdel

> 修改用户组的权限信息和名字  groupmod   

-g 改id  -n 改名字

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022183433827.png" alt="image-20201022183433827" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022183444751.png" alt="image-20201022183444751" style="zoom:50%;" />

> 用户切换用户组
>
> newgrp指令类似login指令，它是以相同的帐号，另一个群组名称，再次登入系统。欲使用newgrp指令切换群组，您必须是该群组的用户，若不指定群组名称，则newgrp指令会登入该用户名称的预设群组。

```bash
# 登录当前用户
  newgrp root
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022183921842.png" alt="image-20201022183921842" style="zoom:50%;" />



> 拓展：文件的查看



/etc/passwd

这个文件的每一行都代表一个用户，我们可以从这里看出这个用户的主目录在哪里，可以看到属于哪一个组。

登录口令：把真正加密后的用户口令字存放到/etc/shadow,保证我们密码的安全性

用户组的信息都放在/etc/group文件中

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022185512744.png" alt="image-20201022185512744" style="zoom:50%;" />

```bash
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:995::/var/lib/chrony:/sbin/nologin
vagrant:x:1000:1000:vagrant:/home/vagrant:/bin/bash
vboxadd:x:997:1::/var/run/vboxadd:/bin/false
cqp:x:1001:1001::/home/cqp:/bin/bash
```



### 磁盘管理

>df (列出文件系统整体的磁盘使用量)  du (检查磁盘空间使用量)

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022190735262.png" alt="image-20201022190735262" style="zoom:50%;" />





<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022190914318.png" alt="image-20201022190914318" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022191035650.png" alt="image-20201022191035650" style="zoom:50%;" />

 

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022191801186.png" alt="image-20201022191801186" style="zoom:50%;" />



> mac或者linux挂载我们的一些本地磁盘或者文件！
>
>  mount 挂载命令

 

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022192520603.png" alt="image-20201022192520603" style="zoom:50%;" />

> 卸载  umount  -f 【挂载位置】强制



除此之外 以后如果安装了jdk 其实可以使用java中的一些命令来查看信息





### 进程管理

对于我们开发人员来说  linux偏向使用即可

> 基本概念

1.在linux中，每一个程序都是一个进程，每一个进程都有一个id号

2.每一个进程，都会有一个父进程

3.进程可以有两种存在方式  前台运行  后台运行

4.服务一般都是后台运行，基本程序都是前台运行

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022195007741.png" alt="image-20201022195007741" style="zoom:50%;" />

> 命令  ps （process status） 查看当前系统中正在执行的各种进程的信息
>
> ps -xx  ：

*  -a 显示当前终端运行的所有的进程信息 （当前的进程）
* -u  以用户的信息显示进程
* -x  显示后台运行进程的参数

```bash
# ps -aux 查看所有进程
ps -aux|grep mysql    查询进程中有关mysql的

#  |  管道符   A|B 把A的结果作B的输入
#   grep   查找文件中符合条件的字符串！
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022195507584.png" alt="image-20201022195507584" style="zoom:50%;" />



ps-ef  查看父进程的信息

```bash
ps -ef|grep mysql

# 看父进程我们一般使用目录树来查看
pstree -pu
   -p  显示父id
   -u  显示用户组

```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201022200339779.png" alt="image-20201022200339779" style="zoom:50%;" />





问题：没有pstree？

centos7上默认没有安装psmisc包.

   

1、在 Mac OS上

   brew install pstree

2、在 Fedora/Red Hat/CentOS

   yum -y install psmisc

3、在 Ubuntu/Debian

   apt-get install psmisc



> 结束进程

kill -9 进程id

java代码死循环了 





### 配置环境



* rpm安装
* 解压缩
* yum



​                 安装jdk

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201023094131365.png" alt="image-20201023094131365" style="zoom:50%;" />

   删除jdk

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201023094601227.png" alt="image-20201023094601227" style="zoom:50%;" />

配置java   （java环境在 /usr 下）

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201023095518890.png" alt="image-20201023095518890" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201023095639498.png" alt="image-20201023095639498" style="zoom:50%;" />

```bash
vim /etc/profile
```

ps:    rpm下载不需要配置环境变量

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201023100859310.png" alt="image-20201023100859310" style="zoom:50%;" />

项目发布必须要保证端口开启 需要查看防火墙  以（9000端口为例）





常见问题:



## linux环境配置



![image-20201210144720048](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210144720048.png)









### mac下，使用idea开发时需要切换项目文件所有者：sudo chown -R chenqipeng:staff xxxx

xxxx为文件名称





## 常用快捷键



sudo fuser -k -n tcp 80   关闭80端口的进程

netstat -tunlp | grep 8080，查询指定端口使用情况

