---
title: Python语言规范
date: '2021-09-06'
tags: ['Python', '笔记']
draft: false
summary: Google的Python语言规范的阅读笔记
---

本篇记录一下自己在阅读 Google 的[Python 语言规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_language_rules/)的阅读笔记，仅供个人记忆，可读性较差

1. Import
   1. 对 package 和 module 进行 import，不要单独 import 函数和类，（可能因为担心有重名的情况
   2. import 一定要使用绝对路径，不要因为处于同一文件夹下面，就使用相对路径（仍然是担心有重名？
2. 异常
   1. assert 一般用于处理函数内部的错误，不要 assert 某个输入参数不规范，用 raise 来做这件事
   2. 永远不要用 except:或者 catch Exception 来捕获所有错误，因为这可能会隐藏真正的错误
   3. 减少 try/catch 的代码量
   4. 使用 finally 来清理资源
3. 全局变量
   1. 避免使用全局变量，但是推荐使用常量
   2. 如果一定要使用，应在模块内部声明并在名称前加\_使之成为模块的内部变量
4. 内部类与内嵌函数
   1. 内部类与内嵌函数应该避免使用，如果只是想对模块外部隐藏函数，应该在名称前加\_
5. 推导式与生成式
   1. 写起来高效，但是会破坏可读性
   2. 应该用在简单情况，每个部分单独一行，并且禁止多重 for
6. 默认迭代器
   1. 推荐使用默认迭代器，而不是默认迭代器与返回列表的方法混用
   2. 推荐 `for line in file`，不推荐`for line in file.readlines()`
7. 生成器
   1. 推荐使用生成器，但是在注释中应用 Yields: 而不是 Returns:
8. lambda 函数
   1. 适用于单行函数
   2. 常见的操作符应该用相应的函数，如乘法最好用 operator.mul
9. 条件表达式
   1. 适用于单行
10. 默认参数
    1. 由于默认参数是在模块加载时求值的，如果以可变对象作为参数的默认值，可能会导致错误
    2. 推荐使用默认参数，但是建议使用不可变对象作为默认值
11. 特性 property
    1. 设置 getter 和 setter
12. False
    1. python 中有隐式 false，0， None，[]等都会被隐式转化成 False，一般情况下应该尽量使用隐式转化
    2. 如果判断对象是否是 None，尽量用 is，因为 is 是比较变量的内存地址，不用 is，可能会出现对象为 False 的情况
    3. 不要用==来判断 False，因为逻辑非常非常复杂，应该用隐式转换来判断
13. 不要使用 python 中过时的语言特性
    1. 使用字符串本身的操作取代 string 库
    2. 使用函数调用的语法取代 apply
    3. 使用列表推导取代 filter/map/reduce
14. 词法作用域
    1. 在作用域范围外的变量可以被引用但是不能被赋值，一旦赋值，会导致 python 将全部引用当作局部变量，甚至是赋值之前的引用
15. 装饰器
    1. 需要谨慎使用，避免使用 staticmethod 和 classmethod
16. 线程
    1. 不要依赖内置数据类型的原子性，这是不被保证的
    2. 使用 Queue 用来进行线程间通信
    3. 使用`threading.Condition`来取代低级别的锁
17. 注释
    1. 对函数的参数和返回值进行注释: `Func(a: int) -> List[int]`
    2. 对变量的声明进行注释: `Val: Sometype = some_func()`
