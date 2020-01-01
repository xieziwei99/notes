[TOC]

## Python

### 语言特性

- python 没有函数重载
- python 没有接口功能
- python 支持多重继承
- 定义默认参数要牢记一点：默认参数必须指向不变对象！
- 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

#### 奇怪的点

- python-正则表达式-{2,3}之间不能有空格

  ```python
  abc{2,3}	# 表示abc出现2次或3次，注意2,3之间不能用空格
  ```

- 关于字典 dict.update

  ```python
  scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
  scores.update(冷面=67, 方启鹤=85)	# 正确
  scores.update('冷面'=67, '方启鹤'=85)	# 错误
  ```

  

#### 切片

```python
# 从第3号元素开始，到第5号元素为止，不包括第5号元素
s = [0,1,2,3,4,5,6,7,8,9]
print(s[3:5])	# 输出：[3, 4]

range(1, 6)		# 1 2 3 4 5
random.randint(1, 6)	# 1~6
```

#### 使列表反转

```python
s = [1,2,3,4,5]
print(s[::-1])
```

#### f-string

```python
for emp in employees:
    print(f'{emp.name}: {emp.get_salary()}元')
```

#### 随机打乱列表顺序

1. 自带函数

   ```python
   random.shuffle(self.cards)  # 随机打乱列表顺序
   ```

2. 自己实现

   ```python
   # 随机打乱传入的列表的顺序
   def my_shuffle(my_list: list):
       n = len(my_list)
       temp = None
       for i in range(n)[::-1]:    # 使列表反转
           temp = randint(0, i)    # a <= N <= b.
           my_list[i], my_list[temp] = my_list[temp], my_list[i]
   ```

#### tuple比较大小规则

```python
tuple比较大小规则：从第一个开始比较，一旦出现结果就停止，如：

In [1]: (1, 1) < (1, 5)
Out[1]: True

In [2]: (2, 1) < (1, 5)
Out[2]: False
```



### 属性

#### `__slots__`属性

- Python允许在定义class的时候，定义一个特殊的`__slots__`变量，来限制该class实例能添加的属性

  ```python
  class Student(object):
      __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
      
  >>> s.score = 99 # 绑定属性'score'
  Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
  AttributeError: 'Student' object has no attribute 'score'
  ```

  



### 装饰器

#### @contextmanager

- 实现 with 结构

- 例如：执行顺序为，从 timer() 的 try 块进入，执行到 yield，接着执行 with 下方的 print 语句，最后进入 finally 块，故这种写法，可以使语句 #3 执行后，打印出其执行的效率

  ```python
  @contextmanager
  def timer():
      try:
          start = perf_counter()	#1
          yield	#2
      finally:
          end = perf_counter()	#4
          print(f'{end - start}秒')	#5
          
  def main():
      with timer():
          print(f'20: {fib(20)}')	#3
  ```



### 创建线程

#### Thread类

```python
# 新建Thread类启动线程
def main2():
    account = Account()
    threads = []

    # 启动100个线程，对同一个account对象调用deposit函数100次
    for _ in range(100):
        t = threading.Thread(target=account.deposit, args=(1,))
        threads.append(t)
        t.start()
    for t in threads:
        t.join()    # 保证100线程运行完，再执行print语句
    print(account.balance)
```

#### 自定义线程类

```python
class AddMoneyThread(threading.Thread):
    """ 自定义一个线程类，继承自threading.Thread """

    def __init__(self, account: Account, amount):
        # 自定义线程的初始化方法中必须调用父类的初始化方法
        super().__init__()
        self.account = account
        self.amount = amount

    def run(self):
        self.account.deposit(self.amount)
        
        
# 通过新建自定义线程类启动线程
def main3():
    account = Account()
    threads = []

    for _ in range(100):
        t = AddMoneyThread(account, 1)
        threads.append(t)
        t.start()
    for t in threads:
        t.join()
    print(account.balance)
```

#### 线程池

```python
# 通过线程池启动线程
def main1():
    account = Account()

    pool = ThreadPoolExecutor(max_workers=10)
    futures = []
    for _ in range(100):
        future = pool.submit(account.deposit, 1)  # 后面的1作为前面函数的参数
        futures.append(future)
    pool.shutdown()
    for future in futures:
        future.result()
    print(account.balance)
```



### 枚举

- 基本用法

  ```python
  # 默认情况下，不同的成员值允许相同。但是两个相同值的成员，第二个成员的名称被视作第一个成员的别名
  # @unique 限制定义枚举时，不能定义相同值的成员
  @unique
  class Suite(Enum):
      """ 花色 """
  
      DIAMOND, CLUB, HEART, SPADE = range(4)
  
      def __lt__(self, other):
          return self.value < other.value
  ```

  