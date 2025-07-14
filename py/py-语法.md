# 一些语法格式
### 类型转换
- int（a, base=16）# str类型的'123'按十六进制转成int，输出291

### print()函数
print()函数的实际语法为：
```
print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
```
- eg:  print(1, 2, 3, 4, sep='#', end='&')
	输出：1#2#3#4&

- print('I love {0} and {1}'.format('bread','butter'))
	花括号作为占位符，可以为空，可以为序列，也可以为关键字参数

- print('%.3f 秒' % timeit.timeit('[1, 2, 3, 4, 5, 6, 7, 8, 9]', number=10000000))
	`timeit`函数的`number`参数表示代码执行的次数。下面的代码中，我们分别创建了保存`1`到`9`的整数的列表和元组，每个操作执行`10000000`次，统计运行时间。

### 格式化输出方式：

#### 1. f-string（推荐，Python 3.6+）：
`name = "老大" score = 99 print(f"{name} 的分数是 {score}")`
#### 2. format 方法：

python

复制编辑

`print("{} 的分数是 {}".format("老大", 99))`

#### 3. 百分号格式（旧写法）：

python

复制编辑

`print("%s 的分数是 %d" % ("老大", 99))`

# 常用数据结构
### [[列表（list）]]

