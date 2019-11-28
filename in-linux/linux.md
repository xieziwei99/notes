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
  
  

### 设置环境变量

1. vim ~/.bashrc
2. export PATH="$PATH:/home/xzw/Documents/idea-201902/bin"
3. source ~/.bashrc



### 查看已安装软件

- `sudo apt list --installed | grep mongodb` 

### 卸载软件

- `sudo apt remove mongodb` 