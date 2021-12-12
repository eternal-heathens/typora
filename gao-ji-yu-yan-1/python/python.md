# PYTHON

## 变量和简单的数据类型

### 变量的命名和使用

1、变量名只包含字母下、下划线和数字
2、变量名不能包含空格
3、不能将关键字和函数名用作变量名

### 字符串

1. \t 制表符 \n 换行符
2. 删除左空白、右空白、两端空白 lstrip() rstrip()  strip()
3. 引号括起来的都是字符串，其中引号可以是单引号也可以是双引号

### 数字

1. Python中数字不能和字符串一起赋值给变量，需要.str()方法将其变为字符串

### 注释

1. #标识

## 列表

### 列表 

   1. 用方括号（[]）来表示列表，并用逗号来分隔其中的元素
   2. 在列表的末尾添加元素：append()
   3. 在列表中插入元素motorcycles.insert(num,str),将列表中既有的元素都右移一个位置
   4. del motorcycles[0] 删除0号元素 motorcycles.pop(num)弹出最后一个元素并删除  motorcycles.remove('str')

 #### 组织列表

 cars.sort() 从左按右按顺序排列  cars.sort(reverse = True) 从右向左
 临时排序 sorted(cars) 
 倒转排序 cars.reverse()
 确认列表长度len(cars)

* 访问最后一个元素可以用索引 -1 （列表为空时会报错）

### 操作列表

遍历：for magician in magicinas:
for循环后面，没有缩进的代码只执行一次

* python 根据缩进来判断与前一个代码行的关系

range(num,num) 生成一系列数字(实质上应是一个数组)

#### 列表解析

 eg;
squares = [value**2 for value in range(1,11)]
print(squares)

#### 切片

eg: print(players[0:3])
  print(players[-3:])

复制列表：同时省略起始和终止索引，如果直接赋值，将是两个变量同时指向同一个列表，不是复制副本

#### 元组

 Python 将不能修改的值称为不可变的，而不可变的列表称为元组

* 若要修改元组变量需重新定义元组

* 使用圆括号而不是方括号来标识

#### 设置代码格式

1. 建议每级缩进都使用四个空格
2. 混合使用了制表符和空格符，可将文件中所有的制表符转换为空格。
3. 建议每行不超过80个字符

### IF

   使用and表示&& 
   使用or表示||
   检查特定的值是否包含在列表中，可使用关键词in (不在：not in )
   eg: 

      'mushrooms' in requested_toppings

  三个结构：
	1. if
	2. if-else
	3. if-elif-else

if  格式 ： 在比较运算符两边加一个空格

### 字典

#### 字典的增删查改
eg:

     初始化：alien_0 = {'color':'gree','points':5}
     取值：new_points = alien_0['points']
     添加： alien_0['x_poitns'] = 0
          alien_0['y_points'] = 25
     修改: alien_0['color' = 'yellow']
     删除：del alien_0['points']
     遍历：for str,str alien_0.items():
     遍历所有的键： for str in （sorted）alien_0.key()=for str in                        alien_0()
                    遍历字典时，会默认遍历所有的键
     遍历所有的值：for str in alien_0.value() 保留重复值
                 for str in set(alien_0.value()) 设置集合(列表)

#### 嵌套

 1. 字典列表
    将字典存入列表中：     
     ```
      aliens = []
      for alien_number in range(30):
          new_alien = {'color':'gree','point':5,'speed':'slow'}
          aliens.append(new_alien)
        
      for alien in aliens[:5]:
          print(alien)
     
      print("...")
      print("Total number of aliens:"+str(len(aliens)))
     ```

2.在字典中存储列表
  eg： 遍历其中的列表时记得用for
  ```
  pizza = {
    'crust': 'thick',
    'toppings': ['mushrooms','extra cheese'],
  }
      
  ```

3.在字典中存储字典
  eg:
 ``` 
   users = {
      'aeinstein':{
       'first':'ablert',
       'last':'einstein',
       'location': 'princeton',
       },
      ···
      }
 ```


### 用户输入和While循环

var = input(var/str) var将是字符串

var = int(var/str)

 *input() 和 raw_input() 这两个函数均能接收 字符串 ，但 raw_input() 直接读取控制台的输入（任何类型的输入它都可以接收）。而对于 input() ，它希望能够读取一个合法的 python 表达式，即你输入字符串的时候必须使用引号将它括起来，否则它会引发一个 SyntaxError 。* (python3.0中将两个和为input 没有raw_input()了)

while condition :
   operation 

for循环时一种遍历列表的有效方式，但在for循环中不应修改列表（for 中的in 不能当条件用），否则将导致python难以跟踪其中的元素。要在遍历列表的同事对其进行修改，可以用WHILE

``` 
   for var in list
   while var/str in list
```

## 函数

### 定义函数

``` 
   def greet_user():
   ·····
```

### 位置实参和关键字实参

位置实参:基于实参的顺序

```
def describe_pet(animal_type,pet_name):
   ···
   ···
describe_pet("hamster",'harry')
```

关键字实参：传递给函数的名称-值对

```
def describe_pet(animal_type,pet_name)
   ···
   ···
describe_pet(animal_type='hamster',pet_name = 'harry')
```

默认值：给形参指定默认值
· 仍会依据位置实参来排序
```
def describe_pet(pet_name,animal_type ='dog')
   ····
   ···
describe_pet(pet_name = 'willeie')

```

### 返回值(return)

若函数修改列表又需要引用到，可以将列表的副本传递给函数（切片表示法）
```
function_name(list_name)
```
### 传递任意数量的函数(在形参名面前加上*)
位置实参
```
def make_pizza(size,*toppings):
    ···
    ···
make_pizza(16,'····'，'····')
```
关键字实参
```
def make_pizza(size,**uesr_info ):
    ···
    ···
make_pizza(16,'····'，'····')
```
### 将函数存储在模块中

import module_name/module_name.function_name()
导入特定函数
from module_name import function_name_0,function_1,····
指定别名
from module_name import function_name as fn
导入模块中的所有函数
from module_name import *

## 类

### 创建和实用类

```
class Dog():  # 在python中，手写字母大写的名称指的是类

    def _init_(self,name,age): '''初始化调用，形参self必不可少，还必须位于其他形参的前面，每个与类相关联的方法调用都自动传递实参self,它是一个指向实例本身的调用，让实例能够访问类中的属性和方法,我们不需要传递它'''
        self.name = name 
        self.age = age
        
    def sit(self):
        print(self.name.title()+"is now sitting.")
    
    def roll_over(self):
        print(self.name.title()+"rolled over")
        
```

 ### 继承

 创建子类的实例时，python首先需要完成的任务时给父类的所有属性赋值。

``` 
class ElectricCar(Car): #创建子类时，必须在括号内指定父类的名称
   
    def _init_(self,make,model,year):  接受创建car实例所需要的方法
        super()._init_(make,model,year)  调用父类的方法_init_(),让ElectricCar实例包含父类的所有属性
    
```

1. 给子类定义属性的方法和java一致
2. 重写一致
3. 可将实例用作属性

### 导入类

* 导入模块中的类
from module_name import class_name1,class_name2···

* 导入整个模块
import module_name

* 导入模块中的所有类
from module_name import *

## 文件和异常

关键字with不再需要访问文件后将其关闭



