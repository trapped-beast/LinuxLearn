# Linux学习

## 准备工作

### 1.软件的下载

+ 虚拟机VMware  [VMware官网](https://www.vmware.com/)    找不到Download的话就直接百度搜VMware Workstation Pro
+ CentOS  [CentOS官网](https://www.centos.org/)   选择的是DVD.iso后缀的镜像文件（完整版镜像）进行下载

### 2.创建虚拟机

1. 在VMware主页创建新的虚拟机，一路默认选择[^1]。
2. 选择编辑虚拟机设置，内存设为2G，处理器设置按照自身实际情况（比如我的机子，打开任务管理器中的性能选项卡可以看到内核数4[^2]，逻辑CPU8[^3]，应该是单个CPU 4核8线程的机器。虚拟机设置中就可以配置为8个单核CPU[^4]），网络适配器选择NAT模式[^5]。

### 3.安装CentOS

1. 选择编辑虚拟机设置，选择CD/DVD选项卡，在使用ISO映像文件中选择之前下载好的.iso后缀的镜像文件。
2. 点击开启此虚拟机，在安装信息摘要界面对各项依次进行设置。在“软件选择”中选择带GUI的服务器，可以勾选几个需要的附加软件（比如勾选Windows文件服务器、调试工具、基本网页服务器、图形管理工具）；在“安装目的地”中存储配置选自定义，依次添加新的挂载点：/boot 300mb，swap 2GB[^6]，/ 剩下的所有空间，点击完成后接受更改；在“网络和主机名”中打开网络并修改主机名；用户可以展示不创建。

### 4.方便使用可以进行的操作

1. 可以拍摄快照存档应对误操作。
2. 在终端的编辑→首选项中可以对终端字体等进行设置。

---

## 文件系统

### 相关知识

+ Linux下没有C: D:这种盘符的概念，所有的文件都放在根目录下。

+ 非root用户只能操作自己的目录（即文件→其他位置→计算机→home→自己的目录，或文件→主目录）之下的文件。

### 命令

#### cd 切换当前目录（current directory）

+ cd后接要去的目录

+ 输入目录时按TAB键可以自动补全
+ cd后为空是回到主目录，也可以cd ~回到主目录, ~特指主目录
+ . 表示当前目录，.. 表示上级目录

#### pwd    显示当前目录（print working directory）

#### ls 列出文件和目录（list）

+ ls -l 可以显示详情，其中第一项为10个字符，分为四组1-3-3-3，第一组为d说明是个目录，是 - 说明是个文件，第二组表示当前用户的权限，第三组表示同组的用户的权限，第四组表示其他用户的权限（其中r为read，w为write，x为excute）
+ ls -a 可以显示所有文件（即包括以 . 开头的隐藏文件）

#### chmod 修改文件权限（change file mode）

+ chmod *对象* +（或 -）*权限* 文件名，表示某文件对某对象增加或减少某权限，比如 chmod o+w xxx 表示xxx文件对其他人增加写权限,其中的对象还可以是a（所有人all）、u（用户自己user），不加对象默认修改自己的权限

#### mkdir 创建目录（make directory）

+ mkdir后接要创建的目录名称
+ mkdir -p 可以创建多级目录

#### rmdir 删除空目录（remove directory）

+ rmdir后接要删除的空目录
+ 如果接的目录非空，删除失败

#### rm 删除文件或目录（remove）

+ rm后接要删除文件或目录
+ rm -rf 删除目录和其子项（recursive force）

#### cp 复制文件或目录（copy）

+ cp -rf 后接两个参数，第一个是要复制的母体，第二个是要得到的文件或目录名

#### mv 移动（重命名）文件或目录（move）

+ mv后接两个参数，两个都是文件时为把前一个文件改名为后一个文件，前一个是文件后一个是目录表示把文件移到目录下，两个都是目录时分后者存在（把前者移入后者下）和不存在（重命名）

#### tar 归档（tape archive）

+ tar -cvf xxx.tar file1 file2 file3表示把若干个文件打包成xxx.tar文件，其中c为create创建档案，v为verbose显示详情，f为file
+ tar -czvf xxx.tar.gz file1 file2 file3表示归档并压缩
+ tar -xvf xxx.tar表示把xxx.tar解压到当前目录下，也可以用tar -xvf xxx.tar -C xx指定存放的目录，其中x为extract
+ tar -xzvf xxx.tar.gz表示解压缩

#### ln 软链接[^7]（link）

+ ln -s 后接两个参数，第一个是母体，第二个是创建的软链接名称，其中s表示软链接（默认为硬）
+ ls -l 操作可以看到链接指向的母体

---

## 用户管理

### 命令

#### su 切换用户[^8]（switch user）

+ su 后接要切换的用户名，无参数则默认到root账户

#### useradd 添加用户

+ useradd后接要添加的用户名
+ 新添加的用户会出现在home目录下

#### passwd 修改用户密码

+ useradd后接要进行修改密码操作的用户名

#### userdel 删除用户

+ userdel后接要删除的用户名
+ home目录下被删除的用户对应的文件并不会自动删除，可以手动删除

#### groupadd 创建组

+ groupadd后接要创建的组名
+ useradd -g 祖名 用户名，用于添加用户到指定的组
+ usermod -g 祖名 用户名，用于把某用户移到某组

#### groupdel 删除组

+ groupdel后接要删除的组名

---

## Shell

### 相关知识

+ Shell是帮助用户访问操作系统内核服务的一个界面，是一个命令解释器，用C语言编写
+ 脚本就是一系列的指令，可以用文本编辑器修改
+ 一般把不用编译后执行的语言称作脚本语言，但其实脚本语言和编程语言之间没有明确的区分界限
+ Shell脚本是为Shell编写的脚本程序

### 编写Shell脚本

#### 流程

1. 编辑一个文本文件，比如：

```shell
#!/bin/sh
echo "hello,world"
```

其中第一行表示解释用来该脚本的Shell的路径
2. 保存文件，如 hello.sh
3. 添加可执行权限，如`chmod +x hello.sh`
4. 执行程序，如`./hello.sh`[^9]

#### 了解

+ 定义变量：变量名=xxx
+ 使用变量：${变量名}
  
---

## 环境变量

### 命令

#### export 定义环境变量

+ export 后接 变量名=xxx

#### echo 显示环境变量

+ echo 后接 ${变量名}，也可以省略{}

#### printenv 查看所有环境变量

### 用户环境变量

#### 变量配置方法

1. 用文本编辑器打开~/.bash_profile[^10] 或者用命令`gedit ~/.bash_profile`[^11]

2. 定义环境变量，如`export JAVA_HOME=/opt/jdk1.8`，保存并关闭
3. 注销登陆，再次登陆时改环境变量生效

#### 原理

当用户打开终端时，会自动运行.bash_profile，将变量注入到当前环境中[^12]

### 系统环境变量

#### 变量配置方法

1. 在/etc/profile.d目录下创建一个脚本，如：`gedit /etc/profile.d/myprofile.sh`
2. 定义环境变量，保存并关闭
3. 注销登陆，再次登陆时改环境变量生效

#### 原理

系统环境变量定义在/etc/profile下，但我们一般不直接对该文件进行修改，而是在/etc/profile.d下创建一个自定义的脚本[^13]，加载profile文件的时候会检查profile.d文件

### PATH环境变量

PATH是最常见的一个环境变量，用户描述可执行程序的搜索路径。用`echo $PATH`可以输出PATH环境变量，路径之间用冒号隔开

+ /usr/bin 目录下存放所有用户都可以执行的程序(系统预装的，会随着系统升级而改变)
+ /usr/sbin 目录下存放root用户可以执行的程序
+ /usr/local/bin 目录下存放用户自己的可执行程序
+ /usr/local/sbin 目录下存放root用户自己的可执行程序（不会因为系统升级导致同名文件被覆盖)

#### 变量配置方法

1. 在~/.bash_profile或/etc/profile.d目录下加入环境变量，如`export PATH=$PATH:/opt/jdk1.8/bin`，这条命令的意思是在原来的PATH环境变量的基础上追加新路径，用冒号隔开
2. 定义环境变量，保存并关闭
3. 注销登陆，再次登陆时改环境变量生效

---

## 网络

可以在/etc/sysconfig/network-scripts/ifcfg-ensXXX文件中对网络配置信息进行更改，当然也可以在设置->网络中进行。

### 命令

#### inconfig 显示网络设备信息

+ 第一项中的inet就是该虚拟机的ip地址[^14]

#### ping 检查网络连接情况

+ ping 后接 ip地址或域名

---

## vi/vim文本编辑器

### 介绍

+ 虽然有像gedit这样的GUI文本编辑器，但是很多时候Linux是不带GUI的，所以学习需要命令行文本编辑器。
+ vi是老式的字处理器，vim从vi发展而来。
+ vi进行文本编辑效率较低，如过要进行大量的修改建议先在Windows上编辑好再上传到服务器。

### 命令

#### vi 文本编辑

+ vi 后接文件名打开一个文件，按 i 进入输入模式，输入完成按ESC进入命令行模式，按：wq保存退出
+ 在命令行模式下按：set nu 可以显示行号
+ 如果按了 Ctrl + s 会将vi锁住，按 Ctrl + q 可以解锁。

---

## C/C++开发

### 环境搭建

#### 了解

+ GNU是一个自由的操作系统
+ GCC:GNU Compiler Collection(GNU 编译器集合)，它可以编译C、C++、JAV、Fortran、Pascal、Object-C、Ada等语言。
+ gcc是GCC中的GNU C Compiler（C 编译器）[15]
+ g++是GCC中的GNU C++ Compiler（C++编译器）
+ gdb是一个用来调试C/C++的调试器
+ makefile：一个工程中的源文件很多，它们之间的调用关系可能很复杂，makefile定义了一系列的规则来指定哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为 makefile就像一个Shell脚本一样，也可以执行操作系统的命令。
+ CMake：CMake是用来生成makefile的一个工具，它读入所有源文件之后，自动生成makefile。

#### 安装

+ `gcc --version`
+ `g++ --version`
+ `gdb --version`
+ `cmake --version`

以上四条命令分别检查是否安装了gcc、g++、gdb、CMake，如果没有安装的话，根据提示安装即可。

#### 程序编译运行

+ 以CPP文件的编译运行为例，先编写CPP文件如test.cpp，然后用一条命令即可生成可执行文件`g++ test.cpp -o test`。要运行该程序输入`./test`，其中加 ./ 的原因是当前目录一般不在PATH目录中，直接写文件名会找不到该文件。

刚刚这一条命令可以分为四个步骤：

1. 预处理（Preprocessing）：`g++ -E test.cpp -o test.i`，-o 参数是把结果导入目标文件。这一步会对include中的头文件进行替换，并去掉注释
2. 编译（Compiling）：`g++ -S test.i -o test.s`，这一步会对代码进行语法分析，生成汇编代码文件
3. 汇编（Assembling）：`g++ -c test.s -o test.o`，这一步会把汇编代码转换成二进制文件
4. 链接（Linking）：`g++ test.o -o test`，这一步会把库函数和test.o进行链接，生成最终的可执行文件

#### g++重要编译参数

1. -g 编译带调试信息的可执行文件（告诉GCC产生能被GNU调试器GDB使用的调试信息），如`g++ -g test.cpp -o test`
2. -O[n] 优化源代码，一般使用`g++ -O2 test.cpp -o xxx.cpp`
3. -l 后面紧接着库名，如`g++ -lglog test.cpp`意思就是链接glog库。注意这里可以链接的库是在/lib、/usr/lib、/usr/local/lib里面的，如果库文件不在这三个目录里，要用-L后紧接着库文件所在目录名的方式进行链接。
4. -I 指定头文件搜索目录，/usr/include目录不用指定，gcc知道去哪里找，但是如果头文件不在/usr/include里就要用-I指定目录。如`g++ -I/myinclude test.cpp`就是指定去/myinclude找头文件。
5. -Wall 打印警告信息，如`g++ -Wall test.cpp`
6. -w 关闭警告信息，如`g++ -w test.cpp`
7. -std=c++11 设置编译标准，如`g++ -std=c++11 test.cpp`
8. -o 指定输出文件名，如`g++ test.cpp -o test`，这里如果不指定文件名会默认输出为a.out
9. -D 定义宏，如`g++ -DDEBUG test.cpp`定义DEBUG宏，用-DDEBUG来选择开启或关闭DEBUG
10. `man gcc` 查看gcc手册

#### gdb常用调试命令参数

+ gdb 后接要调试的可执行文件名
+ 编译时要加上-g才能用gdb进行调试，如`gcc -g test.cpp -o test`

1. help（h） 查看命令帮助，后面可以接具体命令
2. run（r） 运行文件
3. start 单步执行
4. list（l） 查看源代码，list后接行号表示从第几行开始查看，list 后接函数名查看具体函数
5. next（n） 逐过程调试
6. step（s） 逐语句调试
7. finish 结束当前函数，返回函数调用点
8. info（i） 查看函数内部局部变量的值
9. continue（c） 继续运行
10. print（p） 后可以接变量名，打印值及地址
11. quit（q） 退出gdb
12. break num（b num） 在第num行设置断点
13. info breakpoints（i b） 查看当前设置的所有断点
14. delete breakpoints num（d b num） 删除第num个断点
15. display 后可以接变量名，追踪查看具体变量值
16. enable breakpoints 启用断点
17. disable breakpoints 禁用断点
18. 回车：重复上一命令

[^1]:稍微注意一下选择的是Linux系统，一般都是默认Linux。
[^2]:每个物理CPU可以有一个或多个内核。
[^3]:操作系统利用虚拟技术创造的逻辑CPU。
[^4]:这里我个人的理解是这里对处理器的设置可以把自身机器看作一个黑盒子，它的逻辑CPU有8个，我们可以把它就干脆看成有8个实际的物理CPU，每个CPU内核为1，只要保证最终的 处理器数量*单个处理器的内核数 $\leq$实际的线程数 即可。同理也可以看成 4\*2，1\*8。
[^5]:桥连接模式下虚拟机直接连接物理网络，可视为和母机同等地位；NAT模式下虚拟机对外连接时共享母机的IP地址，外界网络不知道它的存在；仅主机模式下不仅虚拟机仅对母机可见，而且母机不具备NAT功能，所以虚拟机无法连接外部网络。
[^6]:swap空间大小一般为创建虚拟机时设置的内存大小的1到1.5倍之间。
[^7]:软链接类似Windows下的快捷方式，它和源文件是两个不同的文件，它保存的是源文件的路径，删除了源文件它就失效了。而硬链接可以理解为是指向源文件（准确地说是指向源文件指向的对象，因为其实“源文件”本身也是一个指针的角色，和硬链接是平等的）的一个指针，删除了源文件不影响对它的操作。
[^8]:用su命令切换用户只是把当前终端会话变成了root账户
[^9]:这里的文件名前面一定要加上 ./ ，否则很可能执行失败。因为你直接写文件名xxx.sh的话，Linux系统会去PATH里找，而你的当前目录一般是不在PATH里的。
[^10]:文本编辑器->打开，这里面是看不见此文件的，因为它是个隐藏文件，输入名称搜索才可以。
[^11]:gedit是GNOME桌面环境下的一个GUI文本编辑器，GNOME是Linux上最常用的图形桌面环境之一。
[^12]:此配置只对当前用户有效，因为每个用户都有自己的配置文件.bash_profile
[^13]:不同的操作系统可能存放自定义脚本的位置不同，这里是CentOS下的情况
[^14]:Windows里显示网络设备信息的命令为ipconfig，该虚拟机的母机的ip地址信息在VMware Network Adapter VMnet8一项中。
[^15]:其实gcc和g++并不是编译器，它们只是一种驱动器，根据要编译的文件的类型调用对应的GNU编译器。