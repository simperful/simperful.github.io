# Work with python
[TOC]

## 1 Regular Expression

### 1.1 简单的模式匹配

**步骤：**

1. 用 import re 导入正则表达式模块
2. 用 re.compile()函数创建一个 Regex 对象(记得使用原始字符串)
3. 向 Regex 对象的 search()方法传入想查找的字符串。它返回一个 Match 对象
4. 调用 Match 对象的 group()方法,返回实际匹配文本的字符串

**for example:**

source code:

~~~
import re
phoneNumRegx = re.compile(r'\d{3}-\d{3}-\d{4}')
mo = phoneNumRegx.search('My phone number is 415-555-4242')
print('phone number found: ' + mo.group())
~~~

output:

~~~
phone number found: 415-555-4242
~~~



### 1.2 匹配更多模式

#### 1.2.1 括号分组

用括号将正则表达式分组，第一对括号是第一组，第二对括号是第二组。

如将上面的正则表达式里面修改为：**(\d{3})-(\d{3}-\d{4})**，则可以向 mo 对象传入参数，传入 1 则只获取第一个括号内的内容，传入 2 则只获取第二个括号内的内容，传入 0 或者不填则获取全部。

如果想要一次就获取所有的分组,可以使用 groups()方法。

**example code:**

~~~
import re

phoneNumRegx = re.compile(r'(\d{3})-(\d{3}-\d{4})')
mo = phoneNumRegx.search('My phone number is 415-555-4242')
areaNumber, mainNumber = mo.groups()
print(areaNumber)
print(mainNumber)
~~~

**output:**

~~~
415
555-4242
~~~



#### 1.2.2 用管道匹配多个分组

字符“|”称为管道。就相当于逻辑里面的“或”。如：正则表达式 r'Batman|Tina Fey'将匹配'Batman'或'Tina Fey'。如果 Batman 和 Tina Fey 都出现在被查找的字符串中,第一次出现的匹配文本,将作为 Match 对象返回。

也可以使用管道来匹配多个模式中的一个,作为正则表达式的一部分。

**example code:**

~~~
import re

heroRegx = re.compile(r'Batman|Tina Fey')
mo1 = heroRegx.search(r'Batman and Tina Fey.')
mo2 = heroRegx.search(r'Tina Fey and Batman.')
print(mo1.group())
print(mo2.group())

batRegx = re.compile(r'Bat(man|mobile|copter|bat)')
mo3 = batRegx.search(r'Batmobile lost a wheel')
print(mo3.group())
print(mo3.group(1))
~~~

**output:**

~~~
Batman
Tina Fey
Batmobile
mobile
~~~



#### 1.2.3 用问号实现可选匹配

字符 ? 表明它前面的**分组**在这个模式中是可选的。就是说,不论这段文本在不在,正则表达式都会认为匹配。换句话说，你可以认为?是在说：“匹配这个问号之前的分组零次或一次”
。

**example code:**

~~~
import re

batRegx = re.compile(r'Bat(wo)?man')
mo1 = batRegx.search(r'The Adventures of Batman')
mo2 = batRegx.search(r'The Adventures of Batwoman')
print(mo1.group())
print(mo2.group())

phoneRegx = re.compile(r'(\d\d\d-)?\d\d\d-\d\d\d\d')
mo3 = phoneRegx.search(r'My number is 415-555-4242')
mo4 = phoneRegx.search(r'My number is 555-4242')
print(r'My phone number is '+ mo3.group())
print(r'My phone number is '+ mo4.group())
~~~

**output:**

~~~
Batman
Batwoman
My phone number is 415-555-4242
My phone number is 555-4242
~~~



#### 1.2.4 用星号匹配零次或多次

*(称为星号)意味着“匹配零次或多次”, 即星号之前的分组,可以在文本中出现任意次。它可以完全不存在,或一次又一次地重复。

**example code:**

~~~
import re

batRegx = re.compile(r'Bat(wo)*man')
mo1 = batRegx.search(r'The Adventures of Batman')
mo2 = batRegx.search(r'The Adventures of Batwoman')
mo3 = batRegx.search(r'The Adventures of Batwowowowoman')
print(mo1.group())
print(mo2.group())
print(mo3.group())
~~~

**output:**

~~~
Batman
Batwoman
Batwowowowoman
~~~



#### 1.2.5 用加号匹配一次或多次

+(加号)则意味着“匹配一次或多次”，就是说，加号前面的分组必须“至少出现一次”，这不是可选的。

**example code:**

~~~
import re

batRegx = re.compile(r'Bat(wo)+man')
mo1 = batRegx.search(r'The Adventures of Batman')
mo2 = batRegx.search(r'The Adventures of Batwoman')
mo3 = batRegx.search(r'The Adventures of Batwowowowoman')
print(mo1 == None)
print(mo2.group())
print(mo3.group())
~~~

**output:**

~~~
True
Batwoman
Batwowowowoman
~~~



#### 1.2.6 用花括号匹配特定次数

如果想要一个分组重复特定次数,就在正则表达式中该分组的后面,跟上花括号包围的数字。

1. 一个数字：正则表达式(Ha){3}将匹配字符串'HaHaHa', 但不会匹配'HaHa', 因为后者只重复了(Ha)分组两次
2. 指定一个范围（在花括号中写下一个最小值、一个逗号和一个最大值）：正则表达式(Ha){3,5}将匹配'HaHaHa'、 'HaHaHaHa'和'HaHaHaHaHa'
3. 指定一个范围（在花括号中缺省最小值或最大值）：(Ha){3,}将匹配 3 次或更多次实例,(Ha){,5}将匹配 **0** 到 5 次实例

**example code:**

~~~
import re

haRegx = re.compile(r'(Ha){3}')
mo1 = haRegx.search(r'HaHaHa')
mo2 = haRegx.search(r'Ha')
print(mo1.group())
print(mo2 == None)
~~~

**code:**

~~~
HaHaHa
True
~~~