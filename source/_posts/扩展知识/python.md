---
title: python
---

### input() 这个内建函数的功能是接收用户的键盘输入，而后将其作为字符串返回

### print(\*objects, sep=' ', end='\n', file=sys.stdout, flush=True)

### 若干数据类型

> range（等差数列）、list 、tuple（元组）、set（集合）、dictionary（字典），Date Type（日期）等等

### 数据容器

> 分为可变容器（Mutable）和不可变容器（Immutable）。
> 可变的有列表、集合、字典；不可变的有字符串、range() 生成的等差数列、元组、Frozen Set。
> 字符串、由 range() 函数生成的等差数列、列表、元组是有序类型（Sequence Type），而集合与字典是无序的。
> 数据容器里的元素可以被迭代

### python 相同类型的值才能相互运算。

```python
type(range(10))   #range
type([1,2,3])     #list
type((1,2,3))     #tuple
type({1,2,3})     #set
type({'a':1, 'b':2, 'c':3})       #dict
```

### 操作符优先级列表

> [参见官方文档](https://docs.python.org/3/reference/expressions.html#operator-precedence)

### 布尔值操作符

> and、or、not

### 字符串操作符

> 拼接：+ 和 ' '（空格）
> 拷贝：\*
> 逻辑运算：in、not in；以及，<、<=、>、>=、!=、==

```python
'Awesome!' * 3   #Awesome!Awesome!Awesome!
'A' > 'a'        #false
ord('A')         #65
ord('a')         #97
```

<!--more-->

### be considered False:

> constants defined to be false: None and False
> zero of any numeric type: 0, 0.0, 0j, Decimal(0), Fraction(0, 1) #0j 复数 Fraction 分数
> empty sequences and collections: '', (), [], {}, set(), range(0)

### 函数

> 内建函数
> 其他模块里的函数
> 其本身所属类之中所定义的函数

#### 函数参数

> 位置参数 可选位置参数 pow(x,y,z) pow(,z)
> 关键字参数 sorted('abdc', reverse=True)
> 带有 = 的，即为其设定默认值的参数，叫 Keyword Arguments，其它是 Positional Arguments。

### 流程控制

> continue 忽略其后的语句开始下次循环， break 结束当前循环，开始执行循环之后的语句：
> 附加在 for 结尾的 else 语句块，在没有 break 下执行。

### 元组

> List 是可变有序容器，Tuple 是不可变有序容器。
> List 用方括号标识 []，Tuple 用圆括号 标识 ()。
> Tuple 已有的内容不可变，无法 del 比 List 占用更少的内存

```python
# 在唯一的元素后面一定要加一个逗号 ,
c = (2) # 不是元组 int
d = (2,) # 这才是元组 tuple
```

### 文件

> 打开文件，open()，基本模式有 r 和 w；关闭 close()
> 写入[增，改，查]，file.read()、file.write()、file.readline()、file.readlines()、file.writelines()；
> os.path.exists(f.name): os.remove(f.name)[删]，
> 用 with 把相关操作都放入同一个语句块:
> with open('results.txt', 'w') as w:

    with open('words_alpha.txt', 'r') as r:

### 函数

#### 函数名

> 名称不能以数字开头。=>大小写字母和下划线 \_；
> 名称中不能有空格，可使用下划线连接词汇，如，do_nothing，或者 Camel Case，如 doNothing —— 更推荐使用下划线；

#### 接收一系列值的位置参数

> 一个函数中，可以接收一系列值的位置参数只能有一个；并且若是还有其它位置参数存在，那就必须把这个可以接收一系列值的位置参数排在所有其它位置参数之后。

```python
def say_hi(greeting, *names):
    for name in names:
        print(f'{greeting}, {name.capitalize()}!')
say_hi('Hello', 'mike', 'john', 'zeo')
```

#### 接收一系列值的关键字参数

```python
def say_hi(**names_greetings):
    for name, greeting in names_greetings.items():
        print(f'{greeting}, {name}!')
say_hi(mike='Hello', ann='Oh, my darling', john='Hi')
#或者以下
a_dictionary = {'mike':'Hello', 'ann':'Oh, my darling', 'john':'Hi'}
say_hi(**a_dictionary)
```

#### 参数顺序

> Positional
> Arbitrary Positional //一系列值的位置参数
> Keyword
> Arbitrary Keyword //一系列值的关键字参数

### 总结

> 数据：整数、布尔值；操作符；变量、赋值；表达式
> 流程控制、分支、循环
> 程序：语句、注释、语句块
> 函数、子程序、参数、返回值、调用
> 数据 操作符 运算(值运算,逻辑运算) 流程控制
