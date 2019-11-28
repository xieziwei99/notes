[TOC]

#### Python

##### 切片

```python
# 从第3号元素开始，到第5号元素为止，不包括第5号元素
s = [0,1,2,3,4,5,6,7,8,9]
print(s[3:5])	# 输出：[3, 4]

range(1, 6)		# 1 2 3 4 5
random.randint(1, 6)	# 1~6
```

##### 使列表反转

```python
s = [1,2,3,4,5]
print(s[::-1])
```

##### python 没有函数重载

##### python 没有接口功能

##### python 支持多重继承

##### 定义默认参数要牢记一点：默认参数必须指向不变对象！

##### 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

##### python-正则表达式-{2,3}之间不能有空格

```python
abc{2,3}	# 表示abc出现2次或3次，注意2,3之间不能用空格
```

##### f-string

```python
for emp in employees:
    print(f'{emp.name}: {emp.get_salary()}元')
```

##### 奇怪

```python
scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
scores.update(冷面=67, 方启鹤=85)	# 正确
scores.update('冷面'=67, '方启鹤'=85)	# 错误
```