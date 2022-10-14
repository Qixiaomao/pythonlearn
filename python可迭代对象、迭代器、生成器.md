# python基础

## 推导式

## 可迭代对象与迭代器、生成器
什么是可迭代对象？
* 问：如何判断一个对象是不是可迭代对象
* 答：有两种方法
* 方法一：isinstance(obj,Iterable)
* 方法二：看有没有__iter__方法

```
def my_range(stop):
    """模拟了Python2中的range"""
    value = 1
    result = []
    while value < stop:
        result.append(value)
        value += 1
    return result


nums = my_range(5)
for num in nums:
    print(num)

for num in nums:
    print(num)
# Python2 中 有 xrange和 range函数
# Python3 中 只有 range

if __name__ == '__main__':
    print(my_range(10))  # 1 2 3 4 5 6 7 8 9
    print(isinstance(my_range(10), Iterable))
    # print(my_range(999999999999999999999999999999999))
```

### 迭代器
* 问：如何判断某个对象是不是迭代器
* 答：有两种方法
* 方法一： isinstance(obj,Iterator)
* 方法二： 看对象有没有 __iter__属性和__next__属性
*  迭代器的协议：
*  1.迭代器类型必须实现__iter__和 __next__（Python2中是next)
*  2. __iter__方法必须 返回 self
*  3. __next__必须返回下一个值，如果没有下一个则抛出StopIterator异常
* from typing import Iterator  # list  tuple  dict  int

* isinstance 是内置函数

```
obj = iter(range(1, 2))  # 把range(1,2)转换为Iterator类型
for attr in dir(list):
    print(attr)  # __iter__

for attr in dir(obj):
    print(attr)  # __iter__   ...  __next__
print(isinstance([1, 2], list))  # True
print(isinstance(obj, Iterator))  # True
print(isinstance([1, 2], Iterator))  # False
```
#### 手动实践一个迭代器
```
#  迭代器的协议：
#  1.迭代器类型必须实现__iter__和 __next__（Python2中是next)
#  2. __iter__方法必须 返回 self
#  3. __next__必须返回下一个值，如果没有下一个则抛出StopIteration异常
#  4. 对迭代器进行for操作时，每次操作都会执行__next__方法
#  5. 只能迭代一遍。
#  6. for语句的迭代，会忽略StopIteration异常
#  7. 迭代器 与 list相比，迭代器省内存

# range(1,5)  1,2,3,4
# Next(5)  1 2  3 4
class Next(object):
    def __init__(self, stop, start=0):
        self.start = 0
        self.stop = stop

    def __iter__(self):
        return self

    def __next__(self):
        """如果有下一个数，则返回下一个数；如果没有下一个数，则抛出StopIteration异常"""
        if self.start >= self.stop - 1:
            raise StopIteration
        self.start += 1
        return self.start


if __name__ == '__main__':
    obj = Next(5)
    for i, value in enumerate(obj):
        print(i, value)
    for i in obj:
        print(i)
    print(obj.__next__())  # 异常
    print(obj.__next__())
    print(obj.__next__())
    print(obj.__next__())
    # print(obj.__next__())  #

```

### 生成器
* 生成器的意义： 为了快速方便地创建一个迭代器，所以生成器一定是一个迭代器
* yield的关键字，来实现快速创建迭代器
* yield怎么用？  在函数中用
* 如果一个函数中有yield关键字，调用函数的时候不会执行函数的内容，会返回一个对象（这个对象类型是生成器类）

```
# 手动实现 平方，  传参（1,3)   返回：1  4  9
result = []
for i in [1, 2, 3]:
    result.append(i * i)
print(result)


class Squares(object):
    def __init__(self, start, stop):
        self.start = start
        self.stop = stop

    def __iter__(self):
        return self

    def __next__(self):
        if self.start > self.stop:
            raise StopIteration
        current = self.start * self.start
        self.start += 1
        return current


def squares(start, stop):  # 第二种
    for i in range(start, stop + 1):
        yield i * i


squares2 = (i * i for i in range(1, 4))  # 第三种

print(type(squares(1, 4)))  # <class 'generator'>
print(type(squares2))  # <class 'generator'>
if __name__ == '__main__':
    # iterator = Squares(1, 3)
    iterator = squares(1, 3)
    for i, value in enumerate(iterator):
        print(i, value)

```
### 生成器是如何执行的？

```
def f():
    print("开始执行")
    a = 1
    yield a
    print("~~~~~~~~~~~")
    a = 2
    yield a


def f2():
    result = []
    result.append(1)
    result.append(2)
    return result


# 当要访问生成器的__next__方法时，函数会编程running状态，当执行完yield时，函数变成非running状态（即挂起），
# 只有再次执行生成器对象的__next__方法时函数才会被唤醒。
# 想一想： 什么情况下会执行生成器对象的__next__方法呢？（获取生成器下一个值的时候）
if __name__ == '__main__':
    obj = f()  # 如果一个函数中有yield关键字，调用函数的时候不会执行函数的内容，会返回一个对象（这个对象类型是生成器类）
    for i, value in enumerate(obj):
        print(i, value)
    # print(obj.__next__())
    # print(obj.__next__())
    # print(obj.__next__())
    print(type(obj))
    print(hasattr(obj, "__next__"))
    print(hasattr(obj, "__iter__"))
# yield  return 区别
# 共同点：都是Python的关键字
# 不同点：return 是结束函数并返回值，yield是暂时离开函数
```
