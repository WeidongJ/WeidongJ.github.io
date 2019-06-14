---
title: python基础知识复习
date: 2019-06-08 09:39:01
tags:
    - python
    - 基础知识
---
## 数据类型和变量

* python中int没有大小限制， 无限大可以使用`inf`来表示；常量通常使用大写字母表示。
* `utf-8`是可变长编码：可以根据一个Unicode字符根据字节大小编码成1-6个字节；计算机中编码的工作方式如下：

    用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件:
{% asset_img encode.png encode %}
* `ord()`函数可以获取字符的整数表示，`chr()`可以把编码转换成对应的字符。
* bytes类型的数据用`b'ABC'`表示，与字符串`ABC`的差别是bytes类型每个字符只占一个字节。把字符串转换成bytes型使用`encode()`方法，使用`decode()`可以把bytes转换成字符串。

        >>> '中文'.encode('utf-8')
        b'\xe4\xb8\xad\xe6\x96\x87'
* dict的key必须是不可变对象，因为dict是根据key来计算存储位置，key可变，dict内部就全乱了。
* **zip()**：函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表，如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。

        >>>a = [1,2,3]
        >>> b = [4,5,6]
        >>> c = [4,5,6,7,8]
        >>> zipped = zip(a,b)     # 打包为元组的列表
        [(1, 4), (2, 5), (3, 6)]
        >>> zip(a,c)              # 元素个数与最短的列表一致
        [(1, 4), (2, 5), (3, 6)]
        >>> zip(*zipped)          # 与 zip 相反，*zipped 可理解为解压，返回二维矩阵式
        [(1, 2, 3), (4, 5, 6)]

## 函数

函数名其实就是指向一个函数对象的引用，完全可以把函数名赋给一个变量，相当于给这个函数起了一个“别名”：

    >>> a = abs # 变量a指向abs函数
    >>> a(-1) # 所以也可以通过a调用abs函数
    1

### 函数的参数

默认参数

    def power(x, n=2):
        s = 1
        while n > 0:
            n = n - 1
            s = s * x
        return s

默认参数必须指向不变对象：Python函数在定义的时候，默认参数L的值就被计算出来了，即[]，因为默认参数L也是一个变量，它指向对象[]，每次调用该函数，如果改变了L的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的[]了。

    def add_end(L=[]):
        L.append('END')
        return L

    >>> add_end()
    ['END', 'END']
    >>> add_end()
    ['END', 'END', 'END']

可变参数：
    def calc(*numbers):
        sum = 0
        for n in numbers:
            sum = sum + n * n
        return sum
*nums表示把nums这个list的所有元素作为可变参数传进去。这种写法相当有用，而且很常见：

    >>> nums = [1, 2, 3]
    >>> calc(*nums)
    14

递归：函数内部调用自己本身。

使用递归需要注意防止栈溢出。可以使用尾递归优化，尾递归即调用自己本身时 return语句不包含表达式，效果等同于循环。

    # 汉诺塔移动
    def move(n, a, b, c):
        if n == 1:
            print(a, '-->', c)
        else:
            move(n-1, a, c, b)
            move(1, a, b, c)
            move(n-1, b, c, a)

### 函数高级特性

python内置函数`enumerate()`可以把列表变成一个索引-元素对。

    >>> for i, value in enumerate(['A', 'B', 'C']):
    ...     print(i, value)
    ...
    0 A
    1 B
    2 C

**生成式（generate）**：列表元素根据某种算法推算出来，循环种不对退算后面的数，不需要创建完整的列表，以节省大量的空间；使用()创建一个生成器，列表生成式使用[]，使用next()获取下一个返回值，最后抛出StopIteration异常。

斐波拉契数列：

    # 列表生成式
    def fib(x):
        n, a, b = 0, 0, 1
        while n < max:
            print(b)
            a, b = a, a + b
            n += 1
        return 'done'

generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：

    # 生成器
    def fib(x):
        n, a, b = 0, 0, 1
        while n < max:
            yield b
            a, b = a, a + b
            n += 1
        return 'done'
最难理解的就是generator和函数的执行流程不一样。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，**在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。**

    def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)

    >>> o = odd()
    >>> next(o)
    step 1
    1
    >>> next(o)
    step 2
    3
    >>> next(o)
    step 3
    5
    >>> next(o)
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    StopIteration

杨辉三角：
    def trangles():
        L = [1]
        while True:
            yield L
            L = [1] + [L[a] + L[a+1] for a in range(0,len(L)-1)] + [1]

### 迭代器

* 可以直接作用于for循环的对象统称为可迭代对象：**Iterable**。
* 可以被next()函数调用并不断返回下一个值的对象称为迭代器：**Iterator**。

Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

Python的for循环本质上就是通过不断调用next()函数实现的

## 函数式编程

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。

函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

### 高阶函数

一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数

变量可以指向函数

    >>> f = abs
    >>> f(-10)
    10

### map()/reduce()

`map()`函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的**Iterator**返回。

    >>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
    ['1', '2', '3', '4', '5', '6', '7', '8', '9']

`reduce()`把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是。

    >>> from functools import reduce
    >>> def add(x, y):
    ...     return x + y
    ...
    >>> reduce(add, [1, 3, 5, 7, 9])
    25

`filter()`也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素,返回一个**Iterator**。

`sorted()`排序

    >>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
    ['Zoo', 'Credit', 'bob', 'about']

### 返回函数

高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。

    def lazy_sum(*args):
        def sum():
            ax = 0
            for n in args:
                ax = ax + n
            return ax
        return sum
调用`lazy_sum()`时，返回求和函数；并且每次调用时都返回一个新的函数：

    >>> f = lazy_sum(1, 3, 5, 7, 9)
    >>> f
    <function lazy_sum.<locals>.sum at 0x101c6ed90>

    >>> f1 = lazy_sum(1, 3, 5, 7, 9)
    >>> f2 = lazy_sum(1, 3, 5, 7, 9)
    >>> f1==f2
    False
闭包：我们在函数`lazy_sum`中又定义了函数`sum`，并且，内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量，当`lazy_sum`返回函数`sum`时，**相关参数和变量都保存在返回的函数中**，这种称为“闭包（Closure）”的程序结构拥有极大的威力。

返回的函数并没有立刻执行，而是直到调用了f()才执行。

    def count():
        fs = []
        for i in range(1, 4):
            def f():
                return i*i
            fs.append(f)
        return fs

    f1, f2, f3 = count()
    >>> f1()
    9
    >>> f2()
    9
    >>> f3()
    9
全部都是9！原因就在于返回的函数引用了变量i，但它并非立刻执行。等到3个函数都返回时，它们所引用的变量i已经变成了3，因此最终结果为9。**返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。**

一定要引用循环变量怎么办？方法是再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量后续如何更改，已绑定到函数参数的值不变:
    def count():
        def f(j):
            def g():
                return j*j
            return g
        fs = []
        for i in range(1, 4):
            fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
        return fs # 返回一个列表

计数器：

    def createCounter():
        li = 0
        def counter():
            li += 1
            return li
        return counter
上述代码会报错：当你在当前作用域中的给变量赋值时，该变量将成为该作用域的局部变量，并在外部范围中隐藏任何类似命名的变量。

    def createCounter():
        l = [0]
        def counter():
            l[0] = l[0] + 1
            return l[0]
        return counter

### 装饰器

在代码运行期间动态增加功能的方式，成为**装饰器（Decorator）**；本质上decorator就是一个返回函数的高阶函数。

    # 装饰器
    def log(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('call %s()' % func.__name__)
            return func(*args, **kw)
        return wrapper

    @log
    def now():
        print('Hello World!')

    def log_text(text):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kw):
                print('%s %s()' % (text, func.__name__))
                return func(*args, **kw)
            return wrapper
        return decorator

    @log_text('调用')
    def new_now():
        print('Hello World!')
Python内置的`functools.wraps`，把原始函数的`__name__`属性复制到`wrapper()`中.

偏函数

`functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

    >>> import functools
    >>> int2 = functools.partial(int, base=2)
    >>> int2('1000000')
    64

## 模块

`sys`模块有一个`argv`变量，用list存储了命令行的所有参数。运行python3 hello.py Michael获得的sys.argv就是['hello.py', 'Michael]。

当我们在命令行运行hello模块文件时，Python解释器把一个特殊变量`__name__`置为`__main__`，而如果在其他地方导入该hello模块时，if判断将失败，因此，这种if测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试

    if __name__=='__main__':
    test()

## 面向对象编程

类和实例

和静态语言不同，Python允许对实例变量绑定任何数据，也就是说，对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同。

访问限制

使用`__`限制属性访问，改为增加`get/se`t方法是因为**在方法中可以对参数做检查**。

* `__xxx__` 特殊变量。
* `_xxx` 约定的私有变量：虽然我可以被访问，但是，请把我视为私有变量，不要随意访问。
* `__xx` 私有变量，python解释器把`__xx`对外改变成`_ClassName__xx`

外部设置变量错误，际上这个__name变量和class内部的__name变量不是一个变量！内部的__name变量已经被Python解释器自动改成了_Student__name，而外部代码给bart新增了一个__name变量：

    >>> bart = Student('Bart Simpson', 59)
    >>> bart.get_name()
    'Bart Simpson'
    >>> bart.__name = 'New Name' # 设置__name变量！
    >>> bart.__name
    'New Name'

### 继承和多态

**继承**：子类获得了父类的全部功能。

**多态**：对于一个变量，我们只需要知道它是Animal类型，无需确切地知道它的子类型，就可以放心地调用run()方法，而具体调用的run()方法是作用在Animal、Dog、Cat还是Tortoise对象上，由运行时该对象的确切类型决定，这就是多态真正的威力：调用方只管调用，不管细节，而当我们新增一种Animal的子类时，只要确保run()方法编写正确，不用管原来的代码是如何调用的。这就是著名的“开闭”原则：

    class Animal(object):
    def run(self):
        print('Animal is running...')

    class Dog(Animal):
        def run(self):
            print('Dog is running...')

    def run_twice(animal):
        animal.run()
        animal.run()

    run_twice(Animal())
    run_twice(Dog())

    Animal is running...
    Animal is running...
    Dog is running...
    Dog is running...

* 对扩展开放：允许新增Animal子类。
* 对修改封闭：不需要修改依赖Animal类型的run_twice()等函数。

动态语言vs静态语言

对于静态语言（例如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类，否则，将无法调用run()方法。

对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以了：

    class Timer(object):
        def run(self):
            print('start...')

    run_twice(Timer())

    start...
    start...
这就是动态语言的“**鸭子类型**”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。

### 获取对象信息

`type()`用于判断对象的类型,使用`isinstance()`判断继承关系。

`dir()`获取对象的所有属性，实际上`len()`方法是自动调用该对象的`__len__()`方法

配合`getattr()`、`setattr()`以及`hasattr()`，我们可以直接操作一个对象的状态:

    >>> class MyObject(object):
    ...     def __init__(self):
    ...         self.x = 9
    ...     def power(self):
    ...         return self.x * self.x
    ...
    >>> obj = MyObject()

    >>> hasattr(obj, 'x') # 有属性'x'吗？
    True
    >>> obj.x
    9
    >>> hasattr(obj, 'y') # 有属性'y'吗？
    False
    >>> setattr(obj, 'y', 19) # 设置一个属性'y'
    >>> hasattr(obj, 'y') # 有属性'y'吗？
    True
    >>> getattr(obj, 'y') # 获取属性'y'
    19
    >>> obj.y # 获取属性'y'
    19

### 实例属性和类属性

不可对实例属性和类属性使用相同的名字，因为相同名称的实例属性将屏蔽掉类属性，但是当你删除实例属性后，再使用相同的名称，访问到的将是类属性。

    >>> class Student(object):
    ...     name = 'Student'
    ...
    >>> s = Student() # 创建实例s
    >>> print(s.name) # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
    Student
    >>> print(Student.name) # 打印类的name属性
    Student
    >>> s.name = 'Michael' # 给实例绑定name属性
    >>> print(s.name) # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
    Michael
    >>> print(Student.name) # 但是类属性并未消失，用Student.name仍然可以访问
    Student
    >>> del s.name # 如果删除实例的name属性
    >>> print(s.name) # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
    Student

给Student类增加一个类属性，每创建一个实例，该属性自动增加：

    class Student(object):
        count = 0

        def __init__(self, name):
            self.name = name
            Student.count += 1 # 访问类属性

变量的类型：

1. 全局变量 - 在所有函数和类的外面
2. 局部变量 -  
    1. 在函数内
    2. 在class的方法内，未加 self 修饰
3. 静态变量 - 在class内，并且是在方法外
4. 实例变量 - 在class方法内，用self修饰

## 面向对象高级编程

给实例绑定一个属性：

    class Student(object):
        pass

    >>> s = Student()
    >>> s.name = 'Michael' # 动态给实例绑定一个属性
    >>> print(s.name)
    Michael
给实例绑定一个方法：

    >>> def set_age(self, age): # 定义一个函数作为实例方法
    ...     self.age = age
    ...
    >>> from types import MethodType
    >>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
    >>> s.set_age(25) # 调用实例方法
    >>> s.age # 测试结果
    25
给类绑定一个方法：

    >>> def set_score(self, score):
    ...     self.score = score
    ...
    >>> Student.set_score = set_scor

使用`__slots__`可以限制实例的属性,使用__slots__要注意，__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的。

    class Student(object):
        __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称

`@property`装饰器就是负责把一个方法变成属性调用的:

    class Student(object):

        @property
        def score(self):
            return self._score

        @score.setter
        def score(self, value):
            if not isinstance(value, int):
                raise ValueError('score must be an integer!')
            if value < 0 or value > 100:
                raise ValueError('score must between 0 ~ 100!')
            self._score = value

    >>> s = Student()
    >>> s.score = 60 # OK，实际转化为s.set_score(60)
    >>> s.score # OK，实际转化为s.get_score()
    60
    >>> s.score = 9999
    Traceback (most recent call last):
    ...
    ValueError: score must between 0 ~ 100!
<<<<<<< HEAD
=======
注意：`@property` 赋值时需要使用`_xx`

### 多重继承

Mixln：在设计类的继承关系时，通常主线都是翻译继承下来的，但是如果需要“混入”额外的功能，通过多重继承可以实现。这种设计模式通常称为Mixln。Mixln的目的就是给一个类增加多个功能。

tips：多重继承中的属性或者方法重复，遵循取左原则

    class A(object):

        def __init__(self):
            self.name = 'A'

    class B(object):

        def __init__(self):
            self.name = 'B'

    class C(A, B):
        pass

    class D(B, A):
        pass

    a = C()
    print(a.name)
    b = D()
    print(b.name)

    A
    B

python继承的优先顺序：按照拓扑排序：

在图论中，拓扑排序(Topological Sorting) 是一个 有向无环图(DAG,Directed Acyclic Graph) 的所有顶点的线性序列。且该序列必须满足下面两个条件：

* 每个顶点出现且只出现一次。
* 若存在一条从顶点A到B的路径，那么序列中顶点A在B的前面。
{% asset_img DAG.jpg Topological Sorting %}

它是一个DAG图，那么如何写出它的拓扑顺序呢？这里说一种比较常用的方法：

* 从DAG途中选择一个没有前驱(即没有指向其的箭头)的顶点并输出。
* 从图中删除该顶点和所有以它为起点的有向边。
* 重复1和2直到当前DAG图为空或当前途中不存在无前驱的顶点为止。后一种情况说明有向图中必然存在环。
{% asset_img topo_sorted.webp Topo_sorted %}

python C3算法遍历DAG顺序寻找方法，优先找到即调用。

## 定制类

`__str__()`存储了实例的字符串：

    class Student(object):
        count = 0

        def __init__(self, name):
            self.name = name
            Student.count += 1 # 访问类属性

        def __str__(self):
            return 'Student object (name: %s)' % self.name

    print(Student('wdji'))

    Student object (name: wdji)
直接线是调用的变量还是不好看：

    >>> s = Student('Michael')
    >>> s
    <__main__.Student object at 0x109afb310>
因为直接显示变量调用的不是`__str__()`，而是`__repr__()`，两者的区别是`__str__()`返回用户看到的字符串，而`__repr__()`返回程序开发者看到的字符串，也就是说，`__repr__()`是为调试服务的。

    class Student(object):
        count = 0

        def __init__(self, name):
            self.name = name
            Student.count += 1 # 访问类属性

        def __str__(self):
            return 'Student object (name: %s)' % self.name

        __repr__ == __str__

`__iter__()`一个类想被`for ... in`循环，类似list，tuple就必须实现一个`__iter__()`方法，该方法返回一个迭代对象。

    class Fib(object):

        def __init__(self):
            self.a, self.b = 0, 1

        def __iter__(self):
            return self

        def __next__(self):
            self.a , self.b = self.b, self.a + self.b
            if self.a > 1000:
                raise StopIteration()
            return self.a

    for n in Fib():
        print(n)

    1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
`__getitem__()`:实现像list一样取出按照下表取出元素

    class Fib(object):

        def __getitem__(self, n):
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a

    f = Fib()
    print(f[5]) # 8

实现切片需要判断n的类型：

    class Fib(object):

        def __getitem__(self, n):
            if isinstance(n, int):
                a, b = 1, 1
                for _ in range(n):
                    a, b = b, a + b
                return a
            if isinstance(n, slice):
                start = n.start
                stop = n.stop
                if start is None:
                    start = 0
                a, b = 1, 1
                L = []
                for x in range(stop):
                    if x >= start:
                        L.append(a)
                    a, b = b, a + b
                return L

    f = Fib()
    print(f[5:10]) # [8, 13, 21, 34, 55]
以上没有处理step和负数，所以实现一个`__getitem__()`还有很多工作要做。如果把对象看成dict，`__getitem__()`的参数也可能是一个可以作key的object，例如str。

与之对应的是`__setitem__()`方法，把对象视作list或dict来对集合赋值。最后，还有一个`__delitem__()`方法，用于删除某个元素。通过上面的方法，我们自己定义的类表现得和Python自带的list、tuple、dict没什么区别，这完全归功于动态语言的“鸭子类型”，不需要强制继承某个接口。

`__getattr__()`

调用不存在的属性时会报错，可以用`__getattr__()`实现动态的返回一个属性。

    class Student(object):
        count = 0

        def __init__(self, name):
            self.name = name
            Student.count += 1 # 访问类属性

        def __str__(self):
            return 'Student object (name: %s)' % self.name

        def __getattr__(self, attr):
            if attr == 'age':
                return lambda: 25
            raise AttributeError('\'Student\' object has no attribute \'%s\'' % attr)

    print(Student('wdji'))
    a = Student('wdji2')
    print(a.age()) # 25
    print(a.grade) # AttributeError: 'Student' object has no attribute 'grade'

动态调用的特性可以用在网站的REST API；利用完全动态的`__getattr__()`写出一个链式调用。

    class Chain(object):

        def __init__(self, path=''):
            self._path = path

        def __getattr__(self, path):
            return Chain('%s/%s' % (self._path, path))

        def __str__(self):
            return self._path

        __repr__ = __str__

`__call__()`

定义一个__call__()方法，就可以直接对实例进行调用:

    class Student(object):
        def __init__(self, name):
            self.name = name

        def __call__(self):
            print('My name is %s.' % self.name)

    >>> s = Student('Michael')
    >>> s() # self参数不要传入
    My name is Michael.

`__call__()`还可以定义参数。**对实例进行直接调用就好比对一个函数进行调用一样，所以你完全可以把对象看成函数，把函数看成对象，因为这两者之间本来就没啥根本的区别。如果你把对象看成函数，那么函数本身其实也可以在运行期动态创建出来，因为类的实例都是运行期创建出来的，这么一来，我们就模糊了对象和函数的界限**。

那么，怎么判断一个变量是对象还是函数呢？其实，更多的时候，我们需要判断一个对象是否能被调用，能被调用的对象就是一个Callable对象，比如函数和我们上面定义的带有__call__()的类实例：

    >>> callable(Student())
    True
    >>> callable(max)
    True
    >>> callable([1, 2, 3])
    False
    >>> callable(None)
    False
    >>> callable('str')
    False

    class urls(Chain):
        def __init__(self, path='/users'):
        self.__path = path

        def __getattr__(self, path):
            return urls(('%s/%s' % (self.__path, path)))

        def __call__(self, path):
            return urls(('%s/%s' % (self.__path, path)))

        def __str__(self):
            return self.__path

        __repr__ = __str__
>>>>>>> bc43e54c7435dcc1774fe65ea627bb91ddd8ad71
