```bash
卸载MySQL，首先得知道MySQL的路径。默认的话是在/usr/local文件夹下的。
在系统偏好设置面板中可以看到之前安装的MySQL，此时若想卸载MySQL，可以按照如下步骤来。【之前安装的时候采用的是默认路径的安装，所以符合下面的卸载步骤】
终端下执行
步骤一：切换到~
cd ~

步骤二：打开usr文件
open /usr

步骤三：找到local，进入到local文件夹，然后依次执行如下命令


sudo rm /usr/local/mysql
sudo rm -rf /usr/local/mysql*
sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/My*
rm -rf ~/Library/PreferencePanes/My*
sudo rm -rf /Library/Receipts/mysql*
sudo rm -rf /Library/Receipts/MySQL*
sudo rm -rf /var/db/receipts/com.mysql.*
```

![截屏2020-09-13 上午3.27.13](/Users/chenqipeng/Desktop/截屏2020-09-13 上午3.27.13.png)

# PS：

> 如果你不是默认路径安装的或者忘记了是不是默认路径安装的，那么除了执行上面的命令之外，还要检查以下文件中是否有对应的文件，有的话删除即可。

- 检查/usr/local/Cellar目录是否有mysql文件，有的话删除。
- 检查/usr/local/var 里的mysql文件，有的话删除。
- 检查/tmp 里的mysql.sock、mysql.sock.lock、 my.cnf文件，有的话删除。
- err文件以及pid文件都是在/usr/local/var/mysql中，有的话删除。
- brew安装的安装包存储在/usr/local/Library/Cache/Homebrew，有的话删除。
- 一定要记得执行brew cleanup。





## mysql配置环境变量



输入：cd /usr/local/mysql，回车执行

然后输入：sudo vim .bash_profile，回车执行

需要输入root用户密码。sudo是使用root用户修改环境变量文件。

进入编辑器后，我们先按"i”，即切换到“插入”状态。就可以通过上下左右移动光标，或空格、退格及回车等进行编辑内容了，和WINDOWS是一样的了。

在文档的最下方输入：export PATH=${PATH}:/usr/local/mysql/bin

![img](https://img-blog.csdn.net/20180610010928391?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MDA0NTIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后按Esc退出insert状态，并在最下方输入:wq保存退出(或直接按shift+zz，或者切换到大写模式按ZZ，就可以保存退出了)。

输入：source .bash_profile 回车执行，运行环境变量。

再输入mysql命令，即可使用。

![img](https://img-blog.csdn.net/20180610012209831?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MDA0NTIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





2020-09-14T04:27:48.651023Z 1 [Note] A temporary password is generated for root@localhost: tP?!z_6Y;MX,

If you lose this password, please consult the section How to Reset the Root Password in the MySQL reference manual.





vi ~/.bash_profile

source ~/.bash_profile