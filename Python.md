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

### 方法-功能




## Django

### 创建 app 基础流程

#### 创建项目

```powershell
# 注意：上面的命令最后的那个点，它表示在当前路径下创建项目
django-admin startproject oa .
```

#### 启动项目

``` sh
python manage.py runserver
```

#### 修改系统语言和时区

```python
# 修改项目的配置文件settings.py

# 设置语言代码
LANGUAGE_CODE = 'zh-hans'
# 设置时区
TIME_ZONE = 'Asia/Shanghai'
```

#### 创建应用

```sh
# 一个Django项目可以包含一个或多个应用
# hrs 为应用名称
python manage.py startapp hrs

# 修改项目的settings.py文件，将我们创建的应用hrs添加已安装的项目中
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'hrs',
]
```

#### 显示页面

```python
# 1、首先修改应用目录下的视图文件views.py，增加视图
from django.http import HttpResponse
def index(request):
    # depts_list = 。。。
    return render(request, 'index.html', {'depts_list': depts_list})

# 1.1、在templates文件夹创建模板页index.html
# 一段html代码

# 1.2、修改项目的配置文件settings.py，配置 templates 作为模版文件夹（pycharm帮我做好了）
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]


# 2、在应用目录创建一个urls.py文件并映射URL
from django.urls import path
from hrs import views
urlpatterns = [
    path('', views.index, name='index'),
]

# 3、修改项目目录下的urls.py文件，对应用中设定的URL进行合并
from django.contrib import admin
from django.urls import path, include
urlpatterns = [
    path('admin/', admin.site.urls),
    path('hrs/', include('hrs.urls')),
]
```

#### 增加模型类

1. 配置 MySQL 数据库

   ```python
   # 修改项目的settings.py文件，配置MySQL作为持久化方案
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.mysql',
           'NAME': 'testdb',	# 存在 testdb 数据库
           'HOST': '127.0.0.1',
           'PORT': 3306,
           'USER': 'xzw',
           'PASSWORD': '12345678',
       }
   }
   ```

2. 使用Python 3需要修改**项目目录**下的`__init__.py`文件并加入如下所示的代码，这段代码的作用是将PyMySQL视为MySQLdb来使用，从而避免Django找不到连接MySQL的客户端工具而询问你：“Did you install mysqlclient? ”

   ```python
   # 修改**项目目录**下的__init__.py文件
   import pymysql
   pymysql.install_as_MySQLdb()
   ```

3. 修改库文件，解决 `mysqlclient 1.3.13 or newer is required;you have 0.9.2` 报错信息

   [参考](https://xieziwei99.github.io/other-blogs/django2.2%E7%89%88%E6%9C%AC%E4%B8%ADpymysql%E6%8A%A5%E9%94%99mysqlclient%201.3.13%20or%20newer%20is%20required_django2.2%E6%8A%A5%E9%94%99,pymysql%E6%8A%A5%E9%94%99,mysqlclient%E6%8A%A5%E9%94%99_%E5%AA%9B%E6%B5%8B-CSDN%E5%8D%9A%E5%AE%A2%20(2020-01-04%20%E4%B8%8B%E5%8D%885_04_56).html)

4. Django框架本身有自带的数据模型，为此我们先做一次迁移操作。所谓迁移，就是根据模型自动生成关系数据库中的二维表

   ```sh
   python manage.py migrate
   ```

5. 添加模型类，在 hrs/models.py 中编写

   ```python
   from django.db import models
   
   
   # Create your models here.
   
   class Department(models.Model):
       """ 部门类 """
       # 省略
   
   
   class Employee(models.Model):
       """ 员工类 """
       id = models.IntegerField(primary_key=True, verbose_name='员工编号')
       name = models.CharField(max_length=20, verbose_name='员工姓名')
       job = models.CharField(max_length=10, verbose_name='职位')
       # no more than 99999.99，即总共7为数字，推荐用此种方式表示金额，而不是FloatField
       salary = models.DecimalField(max_digits=7, decimal_places=2, verbose_name='月薪')  
       # 多对一外键关联(自参照)
       # null=True：默认为False，即默认字段为not null
       manager = models.ForeignKey('self', on_delete=models.SET_NULL, null=True, blank=True, verbose_name='主管')
       # 多对一外键关联(参照部门模型)
       # models.PROTECT：当相关联部门被删除是，抛出异常保护部门不被删（当部门还有员工时）
       department = models.ForeignKey(Department, on_delete=models.PROTECT, verbose_name='所在部门')
   
       def __str__(self):
           return self.name
   
       def __repr__(self):
           return self.__str__()
   ```

6. 再次执行迁移操作，先通过模型生成迁移文件，再执行迁移创建二维表

   ```sh
   python manage.py makemigrations hrs
   
   python manage.py migrate
   ```

#### 创建超级管理员账号

```python
python manage.py createsuperuser

# 并注册模型类，编辑hrs/admin.py
from django.contrib import admin
from hrs.models import Emp, Dept

class DeptAdmin(admin.ModelAdmin):
    list_display = ('no', 'name', 'location')
    ordering = ('no', )

class EmpAdmin(admin.ModelAdmin):
    list_display = ('no', 'name', 'job', 'mgr', 'sal', 'comm', 'dept')
    search_fields = ('name', 'job')

admin.site.register(Dept, DeptAdmin)
admin.site.register(Emp, EmpAdmin)
```



### CRUD 操作

#### 增

```python
>>> dept = Dept(40, '研发2部', '深圳')
>>> dept.save()
```

#### 改

```python
# 先拿到 Dept 的对象
>>> dept.name = '研发3部'
>>> dept.save()
```

#### 删

```python
>>> Dept.objects.get(pk=40).delete()
(1, {'hrs.Dept': 1})
```

#### 查

```python
# 查询所有对象
>>> Dept.objects.all()
<QuerySet [<Dept: 研发1部>, <Dept: 销售1部>, <Dept: 运维1部>, <Dept: 研发3部>]>

# 查询单个对象
>>> Dept.objects.get(pk=10)
<Dept: 研发1部>
>>>
>>> Dept.objects.get(no=20)
<Dept: 销售1部>
>>>
>>> Dept.objects.get(no__exact=30)
<Dept: 运维1部>
>>>
>>> Dept.objects.filter(no=10).first()
<Dept: 研发1部>

# 过滤数据
>>> Dept.objects.filter(name='研发3部') # 查询部门名称为“研发3部”的部门
<QuerySet [<Dept: 研发3部>]>
>>>
>>> Dept.objects.filter(name__contains='研发') # 查询部门名称包含“研发”的部门(模糊查询)
<QuerySet [<Dept: 研发1部>, <Dept: 研发3部>]>
>>>
>>> Dept.objects.filter(no__gt=10).filter(no__lt=40) # 查询部门编号大于10小于40的部门
<QuerySet [<Dept: 销售1部>, <Dept: 运维1部>]>
>>>
>>> Dept.objects.filter(no__range=(10, 30)) # 查询部门编号在10到30之间的部门
<QuerySet [<Dept: 研发1部>, <Dept: 销售1部>, <Dept: 运维1部>]>

# 排序数据
>>> Dept.objects.order_by('no') # 查询所有部门按部门编号升序排列
<QuerySet [<Dept: 研发1部>, <Dept: 销售1部>, <Dept: 运维1部>, <Dept: 研发3部>]>
>>>
>>> Dept.objects.order_by('-no') # 查询所有部门按部门编号降序排列
<QuerySet [<Dept: 研发3部>, <Dept: 运维1部>, <Dept: 销售1部>, <Dept: 研发1部>]>

# 数据切片（分页查询）
>>> Dept.objects.order_by('no')[0:2] # 按部门编号排序查询1~2部门
<QuerySet [<Dept: 研发1部>, <Dept: 销售1部>]>

# 更多
>>> Emp.objects.filter(dept__no=10) # 根据部门编号查询该部门的员工
<QuerySet [<Emp: 乔峰>, <Emp: 张无忌>, <Emp: 张三丰>]>
>>>
>>> Emp.objects.filter(dept__name__contains='销售') # 查询名字包含“销售”的部门的员工
<QuerySet [<Emp: 黄蓉>]>
>>>
>>> Dept.objects.get(pk=10).emp_set.all() # 通过部门反查部门所有的员工
<QuerySet [<Emp: 乔峰>, <Emp: 张无忌>, <Emp: 张三丰>]>
#说明1：由于员工与部门之间存在多对一外键关联，所以也能通过部门反向查询该部门的员工（从一对多关系中“一”的一方查询“多”的一方），反向查询属性默认的名字是类名小写_set（如上面例子中的emp_set），当然也可以在创建模型时通过ForeingKey的related_name属性指定反向查询属性的名字。如果不希望执行反向查询可以将related_name属性设置为'+'或以'+'开头的字符串

# Q对象（用于执行复杂查询）的使用：
>>> from django.db.models import Q
>>> Emp.objects.filter(
...     Q(name__startswith='张'),
...     Q(sal__gte=5000) | Q(comm__gte=1000)
... ) # 查询名字以“张”开头且工资大于等于5000或补贴大于等于1000的员工
<QuerySet [<Emp: 张三丰>]>
```



## scrapy

### 安装

```sh
# 需要前置安装 pywin32 和 twisted
pip install pywin32
pip install twisted
pip install scrapy
```

### 使用

scrapy startproject dataspider

scrapy crawl spiderscript



## numpy

1. numpy 中的切片是浅拷贝

   ```python
   arr = np.arange(1, 21).reshape(4, 5)
   print(arr)
   print(arr[1][1])
   arr[2][2] = 100
   print(arr)
   
   arr2 = arr[0:1, 0:1]  # numpy 中的切片是浅拷贝
   print(arr2)
   arr2[0][0] = 1000
   print(arr)
   
   [[ 1  2  3  4  5]
    [ 6  7  8  9 10]
    [11 12 13 14 15]
    [16 17 18 19 20]]
   7
   [[  1   2   3   4   5]
    [  6   7   8   9  10]
    [ 11  12 100  14  15]
    [ 16  17  18  19  20]]
   [[1]]
   [[1000    2    3    4    5]
    [   6    7    8    9   10]
    [  11   12  100   14   15]
    [  16   17   18   19   20]]
   ```



### 函数

#### reshape

```python
# -1 表示缺省值，意味着行数需要 numpy 计算，列数规定为 1
def main():
    a = np.array([1, 2, 3, 4])
    a = a.reshape((-1, 1))
    print(a)
    '''
    [[1]
     [2]
     [3]
     [4]]
    '''
```

















