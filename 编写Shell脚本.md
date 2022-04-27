## 编写Shell脚本

**Shell脚本命令的工作方式**

**交互式（Interactive）**：用户每输入一条命令就立即执行

**批处理（Batch）**：由用户事先编写好一个完整的Shell脚本，Shell会一次性执行脚本中诸多的命令

通过查看SHELL变量可以发现，当前系统已经默认使用Bash作为命令行终端解释器

```bash
[root@iZ2zeb9up93dfcfsx0okcjZ ~]# echo $SHELL
/bin/bash
```

### 编写简单的脚本

使用Vim编辑器把Linux命令按照顺序依次写入到一个文件中，就是一个简单的脚本

查看当前所在工作路径并列出当前目录下所有的文件及属性信息

```bash
[root@linuxprobe ~]# vim example.sh
#!/bin/bash 
#For Example BY linuxprobe.com 
pwd 
ls -al
```

> Shell 脚本文件的名称可以任意，建议加上 .sh 后缀，表示一个脚本文件

第一行的脚本声明（#!）用来告诉系统使用哪种Shell解释器来执行该脚本

第二行的注释信息（#）是对脚本功能和某些命令的介绍信息，方便日后使用及修改

第三、四行的可执行语句也就是我们平时执行的 Linux命令

### 接收用户的参数

当用户执行某一个命令时，输入不同的参数的结果是不同的

```bash
[root@linuxprobe ~]# wc -l anaconda-ks.cfg 
44 anaconda-ks.cfg
[root@linuxprobe ~]# wc -c anaconda-ks.cfg 
1407 anaconda-ks.cfg
[root@linuxprobe ~]# wc -w anaconda-ks.cfg 
121 anaconda-ks.cfg
```

Linux系统中的Shell脚本语言早就考虑到了这些，已经内设了用于接收参数的变量，变量之间使用空格间隔。例如，$0对应的是当前Shell脚本程序的名称，$#对应的是总共有几个参数，$*对应的是所有位置的参数值，$?对应的是显示上一次命令的执行返回值，而$1、$2、$3……则分别对应着第*N*个位置的参数值

```bash
[root@linuxprobe ~]# vim example.sh
#!/bin/bash
echo "当前脚本名称为$0"
echo "总共有$#个参数，分别是$*。"
echo "第1个参数为$1，第5个为$5。"
[root@linuxprobe ~]# bash example.sh one two three four five six
当前脚本名称为example.sh
总共有6个参数，分别是one two three four five six。
第1个参数为one，第5个为five。
```

### 判断用户的参数

系统在执行mkdir命令时会判断用户输入的信息，即判断用户指定的文件夹名称是否已经存在，如果存在则提示报错；反之则自动创建。Shell脚本中的条件测试语法可以判断表达式是否成立，若条件成立则返回数字0，否则便返回非零值

按照测试对象来划分，条件测试语句可以分为4种：**文件测试语句；逻辑测试语句；整数值比较语句；字符串比较语句**

**文件测试即使用指定条件来判断文件是否存在或权限是否满足等情况的运算符**

#### 文件测试所用的参数

| 操作符 | 作用                       |
| ------ | -------------------------- |
| -d     | 测试文件是否为目录类型     |
| -e     | 测试文件是否存在           |
| -f     | 判断是否为一般文件         |
| -r     | 测试当前用户是否有权限读取 |
| -w     | 测试当前用户是否有权限写入 |
| -x     | 测试当前用户是否有权限执行 |

使用文件测试语句来判断 /app 是否为一个目录类型的文件，然后通过Shell解释器的内设 $? 变量显示上一条命令执行后的返回值。如果返回值为 0，则目录存在；如果返回值为非零的值，则意味着它不是目录，或这个目录不存在

```bash
[root@iZ2zeb9up93dfcfsx0okcjZ usr]# [ -d /app ]
[root@iZ2zeb9up93dfcfsx0okcjZ usr]# echo $?
0
```

逻辑语句用于对测试结果进行逻辑分析，根据测试结果可实现不同的效果

使用 && 运算符，当前面的命令执行成功后才会执行后面的命令，因此可以用来判断 /dev/cdrom 文件是否存在，若存在则输出 Exist

```bash
[root@linuxprobe ~]# [ -e /dev/cdrom ] && echo "Exist"
Exist
```

使用 || 运算符，当前面的命令执行失败后才执行它后面的命令，因此可以用来结合系统环境变量 USER 来判断当前登录的用户是否为非管理员身份

```bash
[root@linuxprobe ~]# echo $USER
root
[root@linuxprobe ~]# [ $USER = root ] || echo "user"
[root@linuxprobe ~]# su - linuxprobe 
[linuxprobe@linuxprobe ~]$ [ $USER = root ] || echo "user"
user
```

使用 ！运算符，把条件测试中的判断结果取相反值

```bash
[linuxprobe@linuxprobe ~]$ exit
logout
[root@linuxprobe ~]# [ ! $USER = root ] || echo "administrator"
administrator
```

#### 可用的整数比较运算符

| 操作符 | 作用           |
| ------ | -------------- |
| -eq    | 是否等于       |
| -ne    | 是否不等于     |
| -gt    | 是否大于       |
| -lt    | 是否小于       |
| -le    | 是否等于或小于 |
| -ge    | 是否大于或等于 |

#### 常见的字符串比较运算符

| 操作符 | 作用                   |
| ------ | ---------------------- |
| =      | 比较字符串内容是否相同 |
| !=     | 比较字符串内容是否不同 |
| -z     | 判断字符串内容是否为空 |

