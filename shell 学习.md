

## shell 标志方式

开头写`#!/bin/bash`

## shell 文件的执行方式
- ./example.sh
- 使用绝对路径
- bash example.sh

## shell 基本语法
### 变量
- 临时变量：程序内部定义的作用于自定义作用于
    - 用户自定义变量
        - 由字母或下划线大头，只能包含字母数字下划线 区分大小写
        - 使用变量时，要在变量名钱加上前缀"$"
        - 变量赋值两边没有空格
        - 接受shell 返回 A=`ll`
        - 变量拼接 ${DATE}date
        - set 命令 列出所有的变量
        - 单双引号的区别
            - 单引号质检的内容原封不动地指定给了变量
            - 双引号取消了空格的作用,特殊符号的含义保留
        - 删除变量 unset NAME
        - 位置变量和特殊变量
            - 命令行的第一个字作为**命令名**,跟着命令名后的是**位置参数**
            - $0 表示文件名
            - $n 表示第n个变量
            - 特殊变量
                - $* 这个程序的所有参数
                - $# 这个程序的参数个数
                - $$ 这个程序的PID
                - $! 执行上一个后台程序的PID
                - $? 执行上一个指令的返回值
                - $HOME 
                - $PATH 环境信息
                - $PWD 当前位置


 


- 永久变量：环境变量
    - $PATH
 
- read 命令 输入参数
- expr 整数运算 符号和数字之间要有空格 四则运算


### 表达式

### 判断语句
- 变量测试语句
    - test 测试条件
    - 测试范围 
        - 整数 
            - test int1 -eq int2 相等
            - test int1 -ge int2 大于等于
            - test int1 -gt int2 大于
            - test int1 -le int2 小于等于
            - test int1 -lt int2 小于
            - test int1 -ne int2 不等于
        - 字符串 test str1==str2 test str1!=str2 test str1 test str2
        - 文件 
            - test -d file 是否为目录
            - test -f file 是否为文件
            - test -x file 是否可执行 简写成[ -x file ]
            - test -r file 是否可读
            - test -e file 测试文件是否存在
            - test -s file 是否为空文件

### if表达式
```shell
 if 条件 
    then 语句
fi 
```
; 表示两个命令写在一行 互不影响
```shell
if 语句一 ; then
    表达式
else 语句二 ; then
    表达式
fi
# 复杂if else 语句
if 语句一 ; then 
    表达式
elif 语句二 ; then
    表达式
else 
    表达式
fi
```

多条件
&&  并
|| 或

### case 
- case
- for done
- (())
- 循环嵌套
- 跳出循环:break continue
- Shift 参数左移指令4
- shell 中函数使用方法

#### case 
使用格式:
case 变量 in
字符串1) 命令列表
;;
...
字符串n) 命令列表n
;;
esac

```shell
read op
case $op in
    C)
    echo "select Copy"
    ;;
    D)
    echo "select Delete"
    ;;
    B) 
    echo "select Backup"
    ;;
    *)
    echo "invalide selection"
    ;;
esac

for  in 列表 
do 
命令
done


while 条件 
do 
命令
done






