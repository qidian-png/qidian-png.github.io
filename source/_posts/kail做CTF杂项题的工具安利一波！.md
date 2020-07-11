---
title: kail做CTF杂项题的工具安利一波！
date: 2020-04-08 15:04:05
tags: kail
categories: 技术
photos:  https://cdn.jsdelivr.net/gh/yremp/cdn@2.1.5/img/cover/(2).jpg.webp
---



﻿前言：上周的比赛，知识和工具都补充了一波，着实吓到我这老菜鸟了。

## 1.blind-watermark
这个工具主要是解决盲水印问题。
[克隆到kail里](https://github.com/linyacool/blind-watermark.git)

```
python encode.py --image <image file> --watermark <watermark file> --result <result file>

python decode.py --original <original image file> --image <image file> --result <result file>

Use --alpha to change the alpha (default 5.0).
```
这个工具分为加密（encode）和解密（decode）两部分。
原始文件，带有水印的文件，解出来的文件（或加密出来的文件）
在看不懂我也没办法了。
## 2.F5隐写
F5隐写全称F5-steganography。
1.kail安装命令：`git clone https://github.com/matthewgao/F5-steganography`
2.进入F5-steganography文件夹，空白处打开命令终端。
3.java Extract 图片的绝对路径/123456.jpg -p 123456
**切记是绝对路径，并且  -p  后面是密码。**
4.在文件夹内找到并打开output.txt文件，就会有你想要的答案。
## 3.outguess隐写
kail终端命令输入 git clone https://github.com/crorvick/outguess 进行下载，下载完成后进入outguess文件夹，右击打开终端，
执行命令./configure && make && make install 进行编译及安装。
outguess -k '密钥' -r 图片的绝对路径 flag.txt解密，flag.txt（在outguess文件夹内）中的内容是要隐藏的答案。
outguess -k "secret key" -d flag.txt 0.jpg 1.jpg加密，加密之后，0.jpg会覆盖1.jpg。
## 4.steghide工具
如果你的Kali Linux还没有更新过的话，那么执行以下命令进行更新。
　　apt-get upgrade
　　当系统更新完毕后，就可以采取在线安装的方式把steghide工具装入到你的系统中。apt-get install steghide
　　如果权限不够的话需要加，在命令前面加sudo,然后输入自己的密码就🆗了。
用法示例：
将post.txt文件隐藏到xxx.jpg中：
steghide embed -cf xxx.jpg -ef post.txt -p 123456（不加-p参数 不设置密码 ）
从xxx.jpg解出post.txt:
steghide extract -sf xxx.jpg （图片路径）-p 123456（-p 密码）
steghide也可以用来爆破密码：
需要shell脚本：`#bruteStegHide.sh 

>        #bruteStegHide.sh
>        #!/bin/bash
>     
>     for line in `cat $2`;do
>     steghide extract -sf $1 -p $line > /dev/null 2>&1
>     if [[ $? -eq 0 ]];then
>     echo 'password is: '$line
>     exit
>     fi
>     done



用法：
       # ./bruteStegHide.sh test.jpg passwd.txt
       剩下的密码就出来了。
## 5.文件分离工具（binwalk,foremost）
1.binwalk的安装

```
git clone https://github.com/devttys0/binwalk
cd binwalk
python setup.py install
```
运行命令：

> binwalk -D "archive:zip" -D "image:jpg" hehe.jpg   可以直接分离文件

也可以使用dd命令分离文件，如:`dd if=hehe.jpg of=hehe1.zip bs=1 skip=54163`

2、Binwalk的提取与分析

2.1、固件扫描

　　命令：binwalk firmware.bin      通过扫描能够智能地发现目标文件中包含的所有可识别的文件类型。

2.2、提取文件

　　命令：binwalk -e firmware.bin      选项“-e"和“--extract"用于按照定义的配置文件中的提取方法从固件中提取探测到的文件系统。

　　命令：binwalk -Me firmware.bin      选项“-M"和“--matryoshka"用于根据magic签名扫描结果进行递归提取，仅对"-e"和“--dd"选项有效。

　　命令：binwalk -Me -d 5 firmware.bin      选项“-d"和“--depth=<int>"用于限制递归提取深度，默认深度为8，仅当“-M"选项存在时有效。

　　命令   dd if=（原文件名） of=（提取文件名）skip=数据地址 bs=1

2.3、显示完整的扫描结果

　　命令：binwalk -I firmware.bin      选项"-I"和“--invalid"用于显示扫描的所有结果（即使是扫描过程中被定义为“invalid“的项）。当我们认为binwalk错把有效的文件当成无效文件时，可以通过该选项来检查。

## 6.foremost的使用。
1、foremost在kali linux已经预装。
#apt-get install foremost

2、然后看是否下载成功，输入

> foremost -h

会显示如下信息（已经翻译）

```
$ foremost [-v|-V|-h|-T|-Q|-q|-a|-w-d] [-t <type>] [-s <blocks>] [-k <size>] 
    [-b <size>] [-c <file>] [-o <dir>] [-i <file] 

-V  - 显示版权信息并退出
-t  - 指定文件类型.  (-t jpeg,pdf ...) 
-d  -打开间接块检测 (针对UNIX文件系统) 
-i  - 指定输入文件 (默认为标准输入) 
-a  - 写入所有的文件头部, 不执行错误检测(损坏文件) 
-w  - 向磁盘写入审计文件，不写入任何检测到的文件
-o  - 设置输出目录 (默认为为输出)
-c  - 设置配置文件 (默认为 foremost.conf)
-q  - 启用快速模式. 在512字节边界执行搜索.
-Q  - 启用安静模式. 禁用输出消息. 
-v  - 详细模式. 向屏幕上记录所有消息。

```
ctf文件分离的题目，大多时候可以使用binwalk或者winhex也可以使用dd命令。可以使用foremost，相对来说binwalk更加强大，速度也快，但是有时候如果不能分离出来，就可以试试看foremost。

最简单的使用方法就是将图片放进foremost目录下，输入命令：

```
 foremost -i xxx.jpg
```
在目录下会出现一个output文件夹，分离出的结果会在里面。


工具就说到这，后面肯定还会有！！！
