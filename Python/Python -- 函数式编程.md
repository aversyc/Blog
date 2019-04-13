##### Python -- 函数式编程

**高阶函数：**

1. **map**

   ```python
   # map(function, iterable, ...)
   # 将列表的每个元素传给函数，依次处理并返回一个新的列表
   In [1]: def square(x):
      ...:     return x*x
      ...: 
   
   In [2]: map(square, [1, 2, 3, 4, 5])
   Out[2]: [1, 4, 9, 16, 25]
   ```

2. **reduce**

   ```python
   # reduce(function, iterable[, initializer])
   # 先把列表前两个元素传给函数，将返回得到的值和第三个元素作为两个新的参数传给函数， 又将函数返回值与第四个元素作为两个新的参数传给函数，依次类推，最后返回一个值。
   In [1]: from functools import reduce
   
   In [2]: numbers =  {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
   
   In [3]: def fn(x, y):
      ...:     return x*10 + y
      ...: 
   
   In [4]: def char_to_num(i):
      ...:     return numbers[i]
      ...: 
       
   In [5]: reduce(fn, map(char_to_num, '45315'))		# =reduce(fun, [4, 5, 3, 1, 5])
   Out[5]: 45315
   
   ```

3. **filter**

   ```python
   # filter(function, iterable)
   # 将列表的每个元素传给函数，然后根据返回值是True还是False决定保留还是丢弃该元素
   In [1]: def is_odd(n):
      ...:     return n % 2 == 1
      ...: 
   
   In [2]: filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
   Out[2]: [1, 3, 5, 7, 9]
   ```

4. **sorted**

   ```python
   # sorted(iterable[, cmp[, key[, reverse]]])
   In [11]: numbers = [-1, -5, 7, 3, 1, 8, 0, 2]
   
   In [12]: sorted(numbers)
   Out[12]: [-5, -1, 0, 1, 2, 3, 7, 8]
   
   In [13]: sorted(numbers, key=abs)
   Out[13]: [0, -1, 1, 2, 3, -5, 7, 8]
   
   In [14]: sorted(numbers, reverse=True)
   Out[14]: [8, 7, 3, 2, 1, 0, -1, -5]
   
   In [15]: sorted(numbers, key=abs, reverse=True)
   Out[15]: [8, 7, -5, 3, 2, -1, 1, 0]
   
   ```

**返回函数：**

1. 函数作为返回值

   ```python
   # f = fn() 返回值是函数，必须调用f()计算平方
   In [5]: def fn(*args):
      ...:     def square(x):
      ...:         return x*x
      ...:     def sum():
      ...:         num = 0
      ...:         for x in args:
      ...:             num += square(x)
      ...:         return num
      ...:     return sum
      ...: 
   
   In [6]: fn(1, 2, 3, 4, 5)
   Out[6]: <function __main__.sum>
   
   In [7]: f = fn(1, 2, 3, 4, 5)
   
   In [8]: f()
   Out[8]: 55
   
   In [9]: f1 = fn(1, 2, 3, 4, 5)
   
   In [10]: f2 = fn(1, 2, 3, 4, 5)
   
   In [11]: f1 == f2		# 每次调用都会返回一个新的函数，即使传入相同的参数
   Out[11]: False
   
   In [12]: f1() == f2()
   Out[12]: True
   ```

2. 返回闭包

   ```python
   # 当一个函数返回了一个函数后，其内部的局部变量还被新函数引用
   In [1]: def count():
       ...:     fns = []
       ...:     for x in range(1, 4):
       ...:         def square():
       ...:              return x*x
       ...:         fns.append(square)
       ...:     return fns
       ...: 
   
   # 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。
   In [2]: fn1, fn2, fn3 = count()
   
   In [3]: fn1()
   Out[3]: 9
   
   In [4]: fn2()
   Out[4]: 9
   
   In [5]: fn3()
   Out[5]: 9
       
   
   # 如果要引用循环变量，方法是再创建一个函数，绑定到函数的参数不变
   In [1]: def count():
      ...:     def fn(x):
      ...:         def square():
      ...:             return x*x
      ...:         return square
      ...:     fns = []
      ...:     for x in range(1, 4):
      ...:         fns.append(fn(x))
      ...:     return fns
      ...: 
   
   In [7]: fn1, fn2, fn3 = count()
   
   In [8]: fn1()
   Out[8]: 1
   
   In [9]: fn2()
   Out[9]: 4
   
   In [10]: fn3()
   Out[10]: 9
   ```

3. 匿名函数

   ```python
   # 关键字lambda表示匿名函数，冒号前面的x表示函数参数
   In [1]: f = lambda x: x * x
   
   In [2]: f
   Out[2]: <function __main__.<lambda>>
   
   In [3]: f(3)
   Out[3]: 9
   
   ```

4. 修饰器

   ```python
   # 在函数调用前后自动打印日志，但又不希望修改now()函数的定义，这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）
   In [1]: from time import ctime
       
   In [2]: def log(func):
      ...:     def wrapper(*args, **kw):
      ...:         print('call %s():' % func.__name__)
      ...:         return func(*args, **kw)
      ...:     return wrapper
      ...: 
   
   In [3]: @log
      ...: def now():
      ...:     print(ctime())
      ...: 
   
   In [4]: now()
   call now():
   Thu Apr 11 16:54:13 2019
   
   ```

5. 偏函数

   ```python
   #=======================================
   # functools.partial: 固定函数的某些参数（设置默认值），返回一个新的函数
   #
   # int2 = functools.partial(int, base=2)
   # 等价于 def int2(x, base=2):
   #    		return int(x, base)
   #=======================================
   In [12]: import functools
   
   In [13]: int2 = functools.partial(int, base=2)
   
   In [14]: int2('1010101')
   Out[14]: 85
   
   ```

   
