## 关于命令

- 多命令的间隔符号：
- `&`: 同时执行；
- `;`: 表示分别执行；
- `&&`: 前面成功才执行后面；
- `|`: 前面的结果传给后面；
- `||`: 前面的成功后面不执行，前面失败后面才执行；

## 关于脚本

- 脚本文件格式：通常以`.sh`为后缀；脚本首行为`#!/bin/bash`指定使用哪个shell/interpreter，`#!`（读作 [ʃə'bæŋ]）之后是解释器的路径；

- - 除了使用常用的bash解释器（如[(`/usr/bin/sh`或`/bin/sh`)(`/bin/bash`)(`/usr/bin/csh`)(`/usr/bin/ksh`)(`/sbin/sh`)]），还可以使用例如`#!/usr/bin/env python3`，利用env命令找到python解释器；
  - 提醒：在linux下执行的脚本文件通常需要是unix格式的，（而不是dos格式），可通过vim内命令`set fileformat=dos|unix|mac`转换；在执行前常用`chmod +x`添加执行权限；

## 变量

- 用户变量：使用'='定义，使用'$'读取

- - 使用'='定义，必须紧跟在变量后，不能有空格，否则会识别为一个命令；



- 环境变量：使用`export`定义，使用`set`命令查看当前上下文的环境变量

- - 使用`unset`删除变量

- 位置变量：`$0`~`$9`

- 左移命令: `shift`

- 预定义变量：

- - `$#`: 参数个数；
  - `$*`或`$@`: 参数内容；
  - `$*`与`$@`的不同：在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。例如执行`printf $@`时，如果`$@`包含多个参数，则只会打出第一个值；
  - `$0`: 当前脚本名，值得注意的是它没有包含在`$*`和`$@`
  - `$?`: 上一命令的执行状态；
  - `$$`: 当前 Shell 进程 ID；



- 变量的作用域：

- - 略

- 数组变量：

- - 使用`array=(a b c)`定义数组，元素用空格分隔；通过`${array[index]}`访问元素；
  - 通过`array[index]=value`单独赋值或扩充使用；
  - 通过`unset array[index]`删除数组元素或者`unset array`删除全部数组；
  - 特殊符号: `${array[@]}，${array[*]}`输出所有符号；仅支持1维数组；

## 通配符

- `[abc]`
- `[*?]`
- `[!except]`

略.

## 条件判断

- if表达式：`if <cmd> then ... else ... fi`；`if`要有自己的`fi`，而`elif`不需要有自己的`fi`，下一个`else`或者上一层`if`的`fi`就标识它的结束；

- - 注意：`else`等流程控制的程序块不可以为空；



- `test`和`[ expression ]`命令

- - 使用: `test expr`或者`[ expr ]`, 其中expr是一个表达式;

  - `test`命令后参与比较的参数，如果不用空格分隔，即识别为一个参数；例如`test '1'='2'` 的结果是0，shell实际是在回答：the length of STRING('1'='2') is nonzero，等价于`test -n '1'='2'`;

  - 操作符：

  - - 整数比较：`-eq`, `-ge`, `-gt`, `-le`, `-lt`；

    - 字符串：`=`, `!=`, `-z`, `-n`；

    - - 字符串操作补充：`${#str}`获取字符串长度，`${str: 2: 5}`截取字符串;

    - 文件操作符：`-f`



- `[[ expression ]]`关键字：增强版的test命令

- - 可以直接使用比较符号；变量不需要加双引号；支持逻辑运算符；
  - 支持正则表达式`[[ str =~ <regex> ]]`

- `case-in`语句：`case test_string in case_string1) ...;; case_string2) ...;; ... esac`

- - shell的`case`表达式不存在穿透，即每个分支是自带`break`的效果；

## 循环

- `for <var> do <cmd> done`

- - C风格的`for`循环: `for ((i=1; i<=100; i++)) do ((sum += i)) done`

  - Python风格的`for-in`循环:

  - - `for args` 等价于 `for args in $@`



- `while <expr> do <cmd> done`
- `break`和`continue`命令
- `until <codeA> do <codeB> done`