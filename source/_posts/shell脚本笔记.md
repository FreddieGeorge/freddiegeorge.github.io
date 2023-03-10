---
title: shell脚本笔记
date: 2023-03-02 13:47:08
tags:
- shell
- Linux
categories: 
- Linux
excerpt: shell脚本常用语法、技巧
---

## 前言

最近在工作中编写了几个脚本方便自己的工作流程，在其中也遇到了几个问题，也积累了一些方法和技巧。这篇博客中记录一些shell脚本相关的知识点，但不会包含最基本的内容。


## 条件控制

条件控制是一个脚本程序最基础的一部分

基本语法如下
```shell
if [ command_1 ];then
    # do something
elif [ command_2 ];then
    # do something
else
    # do something
fi
```

我比较喜欢这种写在同一行的写法，看上去比较像c的if和else，这种写法有几个注意事项
* if和`[]`之间要有空格
* `[`的后面和`]`的前面必须要有空格
* if 和 then如果在同一行需要添加 `;`
* `]` 和 `;` 之间不能有空格

接下来是一些比较常用的判断式子，有些我会直接用类似C语言的方式描述他们的作用,参考自[该文章](https://www.jb51.net/article/235932.htm)

### 常用数字判断

|  表达式   | 作用  | 指令含义 |
|  :--:  | :--:  | :--: |
| [ int1 -eq int2 ]  | int1 == int2 | equal |
| [ int1 -ne int2 ]  | int1 != int2 | not equal |
| [ int1 -gt int2 ]  | int1 >  int2 | grearter than |
| [ int1 -ge int2 ]  | int1 >= int2 | greater equal |
| [ int1 -lt int2 ]  | int1 <  int2 | less than |
| [ int1 -le int2 ]  | int1 <= int2 | less equal |

### 常用字符串判断

|  表达式   | 作用  |
|  :--:  | :--:  |
| [ -z str ]  | 如果str为空则返回真 |
| [ -n str ]  | 如果str不为空则返回真 |
| [ str1 == str2 ]  | 字符串是否相等 |
| [ str1 != str2 ]  | 字符串是否不相等 |

### 常用文件判断

文件类型判断

> 该表格内指令都会先判断文件是否存在，如果不存在直接返回假

|  表达式   | 作用  |
|  :--:  | :--:  |
| [ -e FILE ]  | 判断文件或者目录是否存在 |
| [ -b FILE ]  | 判断文件是否为块设备文件 |
| [ -c FILE ]  | 判断文件是否为字符设备文件 |
| [ -d DIR ]  | 判断文件是否为目录文件 |
| [ -f FILE ]  | 判断文件是否为普通文件 |
| [ -L FILE ]  | 判断文件是否为符号链接文件 |
| [ -p FILE ]  | 判断文件是否为管道文件 |
| [ -s FILE ]  | 判断文件是否为空 |
| [ -S FILE ]  | 判断文件是否为套接字文件 |

文件权限判断

> 该表格内指令都会先判断文件是否存在，如果不存在直接返回假

|  表达式   | 作用  |
|  :--:  | :--:  |
| [ -r FILE ]  | 判断文件是否有读权限 |
| [ -w FILE ]  | 判断文件是否有写权限 |
| [ -x FILE ]  | 判断文件是否有执行权限 |

文件之间比较

|  表达式   | 作用  |
|  :--:  | :--:  |
| [ FILE1 -nt FILE2 ]  | 判断文件1修改时间是否比文件2新 |
| [ FILE1 -ot FILE2 ]  | 判断文件1修改时间是否比文件2旧 |
| [ FILE1 -ef FILE2 ]  | 判断文件1是否与文件2的Inode一致，常用于判断硬链接 |

### 逻辑判断

|  表达式   | 作用  |
|  :--:  | :--:  |
| [ cmd1 -a cmd2 ]  | 逻辑与 |
| [ cmd1 -o cmd2 ]  | 逻辑或 |
| [ ! cmd2 ]  | 逻辑非 |


## 路径处理

<!-- 待更新 -->

一个shell脚本需要获取的最关键的路径主要有：shell脚本所在位置的绝对路径，执行脚本的路径。

### 脚本所在位置的绝对路径

shell脚本的路径可以使用dirname来获取，使用也比较简单

```shell
# dirname
SHELL_FOLDER=$(cd $(dirname "$0");pwd)

# readlink
SHELL_FOLDER=$(dirname $(readlink -f "$0"))
```

### 执行脚本的路径

执行脚本的路径直接使用`pwd`即可


## 打印帮助信息

参考自该[文章](https://developer.aliyun.com/article/972038)，这文章讲得很详细了。

我们只需要在开头部分写入三个`###`起始的注释，利用sed指令即可完成打印，编写一个help函数即可,然后根据后面的参数处理部分使用-h即可打印出来

```shell
### Info
### ......

help() {
    sed -rn 's/^### ?//;T;p;' "$0"
}
```

## 参数
 
参数处理要用到的几个特殊字符

|  字符   | 含义  |
|  :--:  | :--:  |
| $# | 传递到脚本的参数个数  |
| $* | 以一个单字符显示所有向脚本传递的参数 |
| $$ | 脚本程序运行的当给钱进程ID号 |
| $! | 后台运行的最后一个进程的ID号 |
| $@ | 显示所有向脚本传递的参数，但是每个参数都加引号 |
| $0 | 脚本文件名称 |
| $n | 第 n 个参数 |
| $? | 最后指令的退出状态。0表示没有任何错误 |
| $- | shell使用的当前选项 |

参数处理最基本的就是使用 $1 $2 等对执行的参数一个个判断，但是这样的问题就是你必须按照顺序给程序输入参数，而且不能对于可变化的参数无法判断。而getopt和getopts可以解决这个问题，getopt是getopts的拓展，这边只讲getopt的使用

getopt的详细用法

我的参数处理模板为(程序参考自[该博客](https://bummingboy.top/2017/12/19/shell%20-%20%E5%8F%82%E6%95%B0%E8%A7%A3%E6%9E%90%E4%B8%89%E7%A7%8D%E6%96%B9%E5%BC%8F(%E6%89%8B%E5%B7%A5,%20getopts,%20getopt)/))

```shell

# 参数处理部分，使用getopt
ARGS=`getopt -o ht --long tar,help -n "$0" -- "$@"`

if [ $? != 0 ]; then
    echo "Terminating..."
    exit 1
fi

# 使用变量方便后续控制流程
OPTION_T=0

#将规范化后的命令行参数分配至位置参数（$1,$2,...)
eval set -- "${ARGS}"

while true
do
    case "$1" in
        -t|--tar) 
            OPTION_T=1
            shift
            ;;
        -h|--help) 
            help
            exit
            shift
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Internal error!"
            exit 1
            ;;
    esac
done

# 处理完opt后，剩下的参数都在$1 $2...

```

## 颜色

这部分其实是echo相关的，利用echo的`-e` 参数输出有颜色的字符，能输出更多样的脚本打印信息，参考自[该文章](https://www.cnblogs.com/lr-ting/archive/2013/02/28/2936792.html)

```shell
    # 字颜色 30-37
　　echo -e “\033[30m 黑色字 \033[0m” 
　　echo -e “\033[31m 红色字 \033[0m” 
　　echo -e “\033[32m 绿色字 \033[0m” 
　　echo -e “\033[33m 黄色字 \033[0m” 
　　echo -e “\033[34m 蓝色字 \033[0m” 
　　echo -e “\033[35m 紫色字 \033[0m” 
　　echo -e “\033[36m 天蓝字 \033[0m” 
　　echo -e “\033[37m 白色字 \033[0m” 
    # 背景色 40-47 [<back-color>;<font-color>m
　　echo -e “\033[40;37m 黑底白字   \033[0m” 
　　echo -e “\033[41;37m 红底白字   \033[0m” 
　　echo -e “\033[42;37m 绿底白字   \033[0m” 
　　echo -e “\033[43;37m 黄底白字   \033[0m” 
　　echo -e “\033[44;37m 蓝底白字   \033[0m” 
　　echo -e “\033[45;37m 紫底白字   \033[0m” 
　　echo -e “\033[46;37m 天蓝底白字 \033[0m” 
　　echo -e “\033[47;30m 白底黑字   \033[0m” 

```

## 函数


函数其实比较简单，只需要按照如下写即可

```shell
function func_name() {
    # do something
    # return value
}
```

而函数也可以通过`$n`的方式传递参数。

## 字符串

shell中字符串有两种表达方法，分别是单引号和双引号,具体区别不再赘述，我一般直接使用双引号字符串。

### 字符串判断

```shell
${var}              # 变量var的值, 与$var相同
${var-DEFAULT}      # 如果var没有被声明, 那么就以$DEFAULT作为其值 *
${var:-DEFAULT}     # 如果var没有被声明, 或者其值为空, 那么就以$DEFAULT作为其值 *
${var=DEFAULT}      # 如果var没有被声明, 那么就以$DEFAULT作为其值 *
${var:=DEFAULT}     # 如果var没有被声明, 或者其值为空, 那么就以$DEFAULT作为其值 *
${var+OTHER}        # 如果var声明了, 那么其值就是$OTHER, 否则就为null字符串
${var:+OTHER}       # 如果var被设置了, 那么其值就是$OTHER, 否则就为null字符串
${var?ERR_MSG}      # 如果var没被声明, 那么就打印$ERR_MSG *
${var:?ERR_MSG}     # 如果var没被设置, 那么就打印$ERR_MSG *
${!varprefix*}      # 匹配之前所有以varprefix开头进行声明的变量
${!varprefix@}      # 匹配之前所有以varprefix开头进行声明的变量
```

### 字符串操作

```shell
${#string}                          # $string的长度 
${string:position}                  # 在$string中, 从位置$position开始提取子串
${string:position:length}           # 在$string中, 从位置$position开始提取长度为$length的子串     
${string#substring}                 # 从变量$string的开头, 删除最短匹配$substring的子串
${string##substring}                # 从变量$string的开头, 删除最长匹配$substring的子串
${string%substring}                 # 从变量$string的结尾, 删除最短匹配$substring的子串
${string%%substring}                # 从变量$string的结尾, 删除最长匹配$substring的子串   
${string/substring/replacement}     # 使用$replacement, 来代替第一个匹配的$substring
${string//substring/replacement}    # 使用$replacement, 代替所有匹配的$substring
${string/#substring/replacement}    # 如果$string的前缀匹配$substring, 那么就用$replacement来代替匹配到的$substring
${string/%substring/replacement}    # 如果$string的后缀匹配$substring, 那么就用$replacement来代替匹配到的$substring
```
## 杂项

shell脚本其实更是一个方便批量处理的工具，许多信息也可以通过linux的指令来完成。

* 获取操作类型: `uanme -s`
