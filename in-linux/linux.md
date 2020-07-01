[TOC]

## 基于 Ubuntu 或 diban 的系统

### Java 相关操作

#### 配置Java环境变量

1. sudo vim /etc/bash.bashrc

2. 在末尾增添

  ```sh
  JAVA_HOME=/usr/local/java/jdk1.8.0_212

  CLASSPATH=.:$JAVA_HOME/bin.tools.jar

  PATH=$JAVA_HOME/bin:$PATH

  export JAVA_HOME CLASSPATH PATH
  ```

3. `source /etc/bash.bashrc` 使更改立即生效



### 文件操作

#### 查看文件大小

- du -sh *

  例如：du -sh idea-IU-192.5728.98/

#### 查看磁盘空间

- df -h

#### 查找文件

```sh
# 在当前目录以及子目录下查找
find | grep wordcount

# 指定查找目录
find /home/xzw/softwares/ | grep wordcount
```

#### 根据内容查找文件

```sh
find input/ | xargs grep "hello"
```



### 设置环境变量

1. vim ~/.bashrc
2. export PATH="$PATH:/home/xzw/Documents/idea-201902/bin"
3. source ~/.bashrc



### apt命令

#### 查看已安装软件

- `sudo apt list --installed | grep mongodb` 

#### 卸载软件

- `sudo apt remove mongodb` 

#### 查看apt中是否有某个软件

- sudo apt-cache search all | grep gcc

#### 查看某软件安装状态

sudo dpkg-query -l virtualbox-6.0



### ssh

1. 远程登录，指定用户名和端口：

   `ssh 用户名@IP地址 -p 端口号`

2. 使用密钥登录：

   ```sh
   # ------第1步------生成SSH密钥
   ssh-keygen	# 执行结果：生成一对公钥私钥，以.pub结尾的为公钥
   
   # ------第2步------将本地公钥添加到服务器中(用户的.ssh目录下)，并改名为authorized_keys
   ssh root@101.37.172.100 -p 22 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
   # 或者使用更简单的命令
   ssh-copy-id xzw@101.37.172.100 -p 22
   
   # ------成功------再次登录不再需要命令
   ssh root@101.37.172.100
   
   # ------配置别名------在~/.ssh/config 里面追加以下内容
   Host root
       HostName 101.37.172.100
       User root
       Port 22
   # 接下来直接使用命令即可登录
   ssh root
   # 可能会报错：Bad owner or permissions on /home/xzw/.ssh/config 
   # 原因是需要执行权限，运行命令
   chmod 755 config
   # 大功告成
   ```

3. 解决ssh连接一段时间后光标无响应问题

   ``` sh
   # ------方法1------修改client端的 etc/ssh/ssh_config 配置文件，添加下列内容
   ServerAliveInterval 60 # client每隔60秒发送一次请求给server，然后server响应，从而保持连接
   ServerAliveCountMax 3  # client发出请求后，服务器端没有响应得次数达到3，就自动断开连接，正常情况下，server不会不响应
   
   # ------方法2------命令行加参数
   ssh -o ServerAliveInterval=60  user@hostname 
   ```

4. scp 传输文件

   ```sh
   # 格式：scp -P port /path/to/local/file user@remote:/path/to/remote/file
   scp -P 22 test.txt root@101.37.172.100:/root
   
   # 可以使用别名
   scp test.txt xzw:~
   
   # 远程下载到本地
   scp xzw:/root/test.txt ./t.txt
   
   # 远程的默认路径是 ~
   
   # 加上 -r 命令可以传送文件夹
   # 下面命令可以把当前目录下的 dir 文件夹传到远程的家目录下
   scp -r dir lab:
   ```

### 用户管理

#### 创建用户

```sh
# ------前提------以root身份登录
useradd -m xzw	# 执行结果：会在/home目录下生成一个xzw目录
passwd xzw
```

#### 更改文件所有权

```sh
sudo chown -R $USER:$USER /var/www/xzw.com/html/	# -R表示递归，$USER:$USER表示当前用户(组)
# 更改文件权限
sudo chmod -R 755 /var/www/xzw.com	
```



------

### Linux文件基本属性

- `-rwxr-xr-x 1 root root 37464685 Jun 28 20:06 springboot.jar`
  ![Linux文件基本属性](images/Linux文件基本属性.png)

### Linux命令
- chgrp：更改文件属组
- chown：更改文件属主
- **chmod**：更改文件属性
  - Linux文件的基本权限有九个，分别是user/group/others三种身份各自的read/write/execute权限
  - 可以使用数字来代表各个权限：r - 4，w - 2，x - 1
  - 或者：chmod u=rwx,g=rx,o=r 文件名         （a代表all）
- touch：创建文件
- cat  由第一行开始显示文件内容
- tac  从最后一行开始显示，可以看出 tac 是 cat 的倒著写！
- nl   显示的时候，顺道输出行号！（nl filename）
- more 一页一页的显示文件内容
- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！(用 -N 显示行号)
- head 只看头几行
- tail 只看尾巴几行
- df：检查文件系统的磁盘空间占用情况

  - df -h：将容量结果以易读的容量格式显示出来
- yum：Yellow dog Updater, Modified是一个在Fedora和RedHat以及SUSE中的Shell前端软件包管理器。

#### 解压

```sh
# ------tar.gz解压------
tar -zxvf file

# ------zip解压------
unzip file
```

#### 删除

```sh
# ------删除目录及其下所有文件------
rm -rf dirname
```

#### 查看端口占用

##### netstat

```sh
# 查看 tcp udp 端口占用
netstat -tunlp | grep 端口号	# -t tcp端口 -u udp端口
```

##### lsof

```sh
# 安装
sudo apt install lsof
# 查看端口占用
sudo lsof -i:80
```



------

### Shell 编程

#### 要点

- **声明变量时，变量名和等号之间不能有空格**

- **使用变量时，要在变量名前加`$**`

- **原生bash不支持简单的数学运算**

  ```sh
  val=`expr 2 + 2`	# 通过expr完成运算
  # 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2
  # 完整的表达式要被 ` ` 包含
  ```

  或者：用**$[]**，其中各处空格有无都行

  [root@izuf683wve4fbnchjdcklsz a]# echo $[a+b]
  4
  [root@izuf683wve4fbnchjdcklsz a]# echo $[ a+b ]
  4
  [root@izuf683wve4fbnchjdcklsz a]# echo $[ a + b ]
  4

  [root@izuf683wve4fbnchjdcklsz a]# echo $[ a * b ]
  8

  **再或者：用c=$(($a+$b))，或 c=$((a+b))**

- readonly a：声明 a 为只读变量

- unset a：删除变量 a ，unset 命令不能删除只读变量

- read：命令从标准输入中读取一行

- **字符串长度**：`${#string}`

- **截取子串**：`${string:5:2}`    => 从第5号开始，截取2个

- 判断字符串是否相等：[ $a = $b ] （也可以用 == ）

- 判断字符串长度是0吗：[ -z $a ] 

- 判断字符串长度不是0吗：[ -n $a ] 

- if条件判断：

  - 要放在方括号之间，并且要有空格，例如: **[$a==$b]** 是错误的，必须写成 **[ $a == $b ]**
  - 或者用 test 命令
  - 或者用 (())，如`if((a==2)); then echo $a; else echo no; fi`  其中空格有无都行（此时没有$，有也可以）

- 条件语句：if then else fi

- 循环语句：

  - for ... in ... do done
  - for((i=1;i<=5;i++))      （此时没有$，有也可以）
  - while do done
  - until do done

- 乘法：

  ```sh
  val=`expr $a \* $b`
  ```

- 关系运算符：只支持数字，不支持字符串，除非字符串的值是数字

  -eq -ne -gt -lt -ge -le

- 布尔运算符：!    -o    -a（也可以是 ||    &&）

- 当前日期：

  ```sh
  echo `date`
  a=`date`
  ```

- bash 支持一维数组（不支持多维数组），并且没有限定数组的大小

  - table=(1 1 2 3 5 8 13)    =>  用()定义数组，（用空格分隔）
  - echo ${table[@]}      =>  @可以获取数组所有元素（用空格分隔）
  - echo ${table[*]}       与@一样
  - echo ${#table[*]}       **获取数组长度**

- **shell** **传参**：$0为文件名，$1 及以后为传入参数，当n>=10时，需要使用${n}来获取参数

  - $#：传递到脚本的参数个数
  - $*：以空格顺序列出的所有参数（相当于一个值）
  - $$：脚本运行的当前进程ID号
  - $!：后台运行的最后一个进程的ID号
  - $@：与$*相同，但是使用时加引号，并在引号中返回每个参数。（相当于n个值）
    如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数
  - $?：显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。

- 多行注释

  ```sh
  :<<EOF
  注释内容...
  注释内容...
  注释内容...
  EOF
  
  :<<'
  注释内容...
  注释内容...
  注释内容...
  '
  
  :<<!
  注释内容...
  注释内容...
  注释内容...
  !
  ```

- let 命令：let 命令是 BASH 中用于计算的工具，用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量，且运算符间不能包含空格，如，let c=a+b

#### 字符串 - 单引号和双引号

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的

- 双引号中可以出现变量，可以有转义

- 单双引号都可以拼接字符串

  ```sh
  greeting="hello, "$your_name" !"	# 等价于greeting_1="hello, ${your_name} !"
  greeting_2='hello, '$your_name' !'
  ```


#### case 语句

```sh
# 取值后面必须为单词in，每一模式必须以右括号结束。取值可以为变量或常数。匹配发现取值符合某一模式后，其间所有命令开始执行直至 ;;。
# 取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

#### 函数

- 返回值由 $? 获得

#### 文件描述符0 1 2

- 文件描述符 0 通常是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）

#### /dev/null 文件

- /dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。

  如果希望屏蔽 stdout 和 stderr，可以这样写：

  ```sh
  $ command > /dev/null 2>&1
  ```

#### 文件包含

- 被包含文件不需要可执行权限

```sh
. filename   # 注意点号(.)和文件名中间有一空格
source filename
```



### Vim

#### 基础命令

1. /word：向下搜索

2. ?word：向上搜索

3. n（或N）：下一个搜索

4. `:1,$s/word1/word2/gc` 或 `:%s/word1/word2/gc`：从第一行到最后一行寻找 word1 字符串，并将该字符串取代为 word2 ！且在取代前显示提示字符给用户确认 (confirm) 是否需要取代！(常用)

5. x（或X）：删除字符

6. dd：删除整行

7. d$：删除游标所在处，到该行的最后一个字符

8. d0：那个是数字的 0 ，删除游标所在处，到该行的最前面一个字符

9. p（或P）：粘贴

10. J：将光标所在行与下一行的数据结合成同一行

11. u：复原前一个动作

12. [Ctrl]+r：重做上一个动作

13. .	不要怀疑！这就是小数点！意思是重复前一个动作的意思。 如果你想要重复删除、重复贴上等等动作，按下小数点『.』就好了

14. ZZ	这是大写的 Z 喔！若档案没有更动，则不储存离开，若档案已经被更动过，则储存后离开！

15. :w [filename]	将编辑的数据储存成另一个档案（类似另存新档）

16. :r [filename]	在编辑的数据中，读入另一个档案的数据。亦即将 『filename』 这个档案内容加到游标所在行后面

17. :n1,n2 w [filename]	将 n1 到 n2 的内容储存成 filename 这个档案。

18. :! command	暂时离开 vi 到指令行模式下执行 command 的显示结果！例如
    『:! ls /home』即可在 vi 当中察看 /home 底下以 ls 输出的档案信息！

19. :set nu	显示行号，设定之后，会在每一行的前缀显示该行的行号

20. :set nonu	与 set nu 相反，为取消行号！

21. G	移动到这个档案的最后一行

22. nG	n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu)

23. gg	移动到这个档案的第一行，相当于 1G 啊！ (常用)

24. 在 10 - 20 行添加 // 注释

    :10,20s#^#//#g

25. 在 10 - 20 行删除 // 注释

    :10,20s#^//##g

26. 在 10 - 20 行添加 # 注释

    :10,20s/^/#/g

27. 在 10 - 20 行删除 # 注释

    :10,20s/#//g

28. 批量注释：

    Ctrl + v 进入块选择模式，然后移动光标选中你要注释的行，再按大写的 I 进入行首插入模式输入注释符号如 // 或 #，输入完毕之后，按两下 ESC，Vim 会自动将你选中的所有行首都加上注释，保存退出完成注释。

    取消注释：

    Ctrl + v 进入块选择模式，选中你要删除的行首的注释符号，注意 // 要选中两个，选好之后按 d 即可删除注释，ESC 保存退出。

##### 复制/剪切

- yy：复制当行
- 3yy：复制三行
- y$：当前到行尾
- y^：当前到行首
- 剪切用 d 代替 y 即可

#### .vimrc配置

```sh
set nocompatible        " 设置与vi不兼容
"set foldmethod=indent   " 设置折叠策略是缩进
set ts=4    " 设置tab宽为4个空格
set expandtab    " 设置tab替换为空格
set nu    " 显示行号
syntax on   " 自动语法高亮
set softtabstop=4    " 使得按退格键时可以一次删掉 4 个空格
set nowrapscan      " 禁止在搜索到文件两端时重新搜索
set incsearch       " 输入搜索内容时就显示搜索结果
set hlsearch        " 搜索时高亮显示被找到的文本
set hidden          " 允许在有未保存的修改时切换缓冲区，此时的修改由 vim 负责保存
set smartindent     " 开启新行时使用智能自动缩进
" set laststatus=2    " 显示状态栏 (默认值为 1, 无法显示状态栏)
" set statusline=\ %<%F[%1*%M%*%n%R%H]%=\ %y\ %0(%{&fileformat}\ %{&encoding}\ %c:%l/%L%)\    " 设置在状态行显示的信息

" 修改快捷键
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>
nnoremap <space> za

" f5执行文件
map <F5> :call RunPython()<CR>
func! RunPython()
    exec "w"
    if &filetype == 'python'
        exec "!time python3 %"
    endif
endfunc
```

#### YouCompleteMe插件

```sh
# ------第1步------安装相关依赖
sudo apt install build-essential cmake vim python3-dev

# ------第2步------使用Vunble安装，配置.vimrc
Plugin 'ycm-core/YouCompleteMe'

# ------第3步------运行安装命令，在vim模式下
:PluginInstall

# ------第4步------在对应目录下执行命令
./install.py --clang-completer

# 试着运行
git submodule update --init --recursive

# ------可能报错1-------
-- Downloading libclang 9.0.0 from https://dl.bintray.com/ycm-core/libclang/libclang-9.0.0-x86_64-unknown-linux-gnu.tar.bz2
CMake Error at ycm/CMakeLists.txt:107 (file):
  file DOWNLOAD HASH mismatch

    for file: [/home/xzw/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/../clang_archives/libclang-9.0.0-x86_64-unknown-linux-gnu.tar.bz2]
# 解决
由于vpn的问题，文件下载不完全，先删除指定文件，再wget指定网址手动下载

# ------第5步------在vimrc中配置
不知道howto，失败了
```

#### Vundle插件

```sh
# ------第1步------安装
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

# ------第2步------配置Vundle，在.vimrc中配置
set nocompatible               "去除VIM一致性，必须
filetype off                   "必须

"设置包括vundle和初始化相关的运行时路径
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

"启用vundle管理插件，必须
Plugin 'VundleVim/Vundle.vim'

"在此增加其他插件，安装的插件需要放在vundle#begin和vundle#end之间
"安装github上的插件格式为 Plugin '用户名/插件仓库名'

call vundle#end()              
filetype plugin indent on      "加载vim自带和插件相应的语法和文件类型相关脚本，必须

# ------运行安装命令，在vim模式下------
:PluginInstall

# ------删除插件------在vim模式下运行
:BundleClean
```

### 更改apt源为阿里云源

```sh
# ------第1步------备份/etc/apt/source.list文件
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

# ------第2步------获取版本号，例如：stable
lsb_release -c

# ------第3步------更改source.list文件
deb http://mirrors.aliyun.com/ubuntu/ stable main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ stable main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ stable-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ stable-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ stable-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ stable-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ stable-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ stable-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ stable-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ stable-proposed main restricted universe multiverse

# ------第4步------更新
sudo apt-get update		# 升级apt
sudo apt-get upgrade	# 升级软件
```

### ufw防火墙命令

```sh
# ------安装------
sudo apt-get install ufw

sudo ufw status
sudo ufw enable
sudo ufw disable
```

## 基于Centos的Linux系统

### yum命令

1. `yum list installed python`：查看已安装的特定软件



### 系统服务

1. 有用户服务和系统服务之分，目录为：/usr/lib/systemd /usr/lib/systemd/system

```sh
[Unit]
Description=Gunicore to start tdd

[Service]
Restart=on-failure
User=root
WorkingDirectory=/root/tdd/sites/123.56.85.195/TDD-TodoList
ExecStart=/root/tdd/sites/123.56.85.195/virtualenv/bin/gunicorn --bind unix:/root/tmp/123.56.85.195.socket superlists.wsgi:application

[Install]
WantedBy=multi-user.target
```











