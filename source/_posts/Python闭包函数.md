---
title: Python闭包函数
date: 2026-8-22 21:25:07
tags: Python
categories: 内容整理
---

## Python闭包函数（Closure）

> 这篇文档假设你已经会写普通的函数，知道什么是变量，但还没听说过"闭包"这个词。
阅读时间约 15 分钟。看完之后，你会明白：闭包是什么、为什么需要它、怎么用它，以及它最容易踩的坑。

---

## 先讲个故事

你出国旅行，出发前在家里把行李箱打包好：两件衣服、一个充电器、一沓现金。

到了国外，你打开行李箱拿东西用。这时候你家（出发前的环境）早就离你十万八千里了，但行李箱里的东西还在，随时能用。

Python 里的**闭包**就是这样一个"带着行李箱的函数"：它把定义时周围的变量打包带走，哪怕后来那个环境已经消失了，这些变量依然跟着它。

这个比喻先记在心里，我们往下看代码就明白了。
<!-- more -->

---

## 第一步：先复习一下普通函数

你肯定写过这样的函数：

```Python
def greet(name):
    return 'hello, ' + name

print(greet('小明'))
```

运行结果：

```Plaintext
hello, 小明
```

很简单，`name` 是参数，函数用它拼了一句话返回。

现在问你一个问题：`name` 这个变量，在函数外面能用吗？

```Python
def greet(name):
    return 'hello, ' + name

print(name)   # 报错！
```

运行结果：

```Plaintext
NameError: name 'name' is not defined
```

报错了。因为 `name` 是函数"内部"的变量，函数外面看不到它。这个"哪些地方能看到哪些变量"的规则，叫做**作用域**。

你可以把作用域想象成房间的墙壁：

```Plaintext
┌──────── greet 函数房间 ─────────┐
        │                                  │
        │   name = '小明'   ← 只在这间屋子里 │
        │   return 'hello, ' + name        │
        │                                  │
        └──────────────────────────────────┘
                          │
          print(name)     │  ← 站在门外，看不见屋里的 name
                          ▼
                    NameError!
```

记住这点，闭包的所有魔法都建立在作用域上。

---

## 第二步：函数里还能再定义函数？

Python 有一个很自然的特性：**函数里面可以再定义函数**。

就像大盒子套小盒子：

```Python
def outer():
    print('外层函数开始执行')

    def inner():
        print('内层函数开始执行')

    print('外层函数准备结束')

outer()
```

运行结果：

```Plaintext
外层函数开始执行
外层函数准备结束
```

咦？`inner()` 那行打印怎么没出现？

因为定义函数（`def inner()`）只是"把函数造出来"，并不会执行它。就像你买了一台洗衣机放在那，没按启动键它就不会转。

要让它转起来，得**调用**它。而且要在 `outer` 内部调用，因为 `inner` 只在 `outer` 这个房间里能看见：

```Python
def outer():
    print('外层函数开始执行')

    def inner():
        print('内层函数开始执行')

    inner()   # ← 在这里调用它

    print('外层函数准备结束')

outer()
```

运行结果：

```Plaintext
外层函数开始执行
内层函数开始执行
外层函数准备结束
```

这次就都有了。

---

## 第三步：见证奇迹的时刻

下面这段代码，请你仔细看，然后猜猜它会输出什么：

```Python
def make_counter():
    count = 0          # 外层函数里的变量

    def counter():
        nonlocal count
        count = count + 1
        return count

    return counter     # 注意：返回的是 counter 这个函数本身，没加括号

my_counter = make_counter()
print(my_counter())
print(my_counter())
print(my_counter())
```

先别急着往下看，停三秒钟猜一下。

好，揭晓答案：

```Plaintext
1
2
3
```

等等——这里有个很反直觉的事情。

`count = 0` 是 `make_counter` 里的局部变量。按理说，`make_counter()` 执行完返回之后，它内部的东西（包括 `count`）就该被销毁了，对吧？

可是 `my_counter` 却能一次又一次地"记住" `count` 的值，从 1 涨到 2 再涨到 3。`count` 好像没死，还活着，而且记得自己上次是几。

这就是**闭包**。

我们回到开头的比喻：`counter` 这个函数就是那个带着行李箱的旅行者。`count` 是它出发前打包进行李箱的现金。`make_counter` 这个"家"虽然已经人去楼空（执行结束了），但 `counter` 带走的行李箱里，`count` 还好好地躺着，每次调用还能往上加一。

用图来表示：

```Plaintext
make_counter() 执行完之后：

    make_counter 的房间  ← 已经拆掉了，count=0 本该消失
              │
              │  但 counter 把 count "打包带走了"
              ▼
    ┌──────────────────────┐
    │   counter 函数        │
    │   ┌──────────────┐   │
    │   │ 行李箱：count │   │  ← 这就是闭包
    │   │   当前值：3   │   │
    │   └──────────────┘   │
    └──────────────────────┘
```

---

## 第四步：到底什么是闭包？

现在可以正式给闭包下个定义了。

**闭包 = 一个函数 \+ 这个函数定义时"看到"的那些变量。**

更准确地说：当一个内部函数引用了它外层函数的变量，并且这个内部函数被返回到外面去用了，就形成了一个闭包。内部函数"封存"住了它依赖的那些外层变量，让它们不会随着外层函数结束而消失。

判断一个东西是不是闭包，看三个条件：

1. 有**嵌套**的函数（函数里套函数）

2. 内层函数**用到了**外层函数的变量

3. 外层函数把内层函数**返回**出去了

三个都满足，就是闭包。

---

## 那个 `nonlocal` 是干嘛的？

你刚才一定注意到了这一行：

```Python
nonlocal count
```

这是什么意思？

Python 有个规则：你在函数里给一个变量赋值，Python 默认认为你是在**创建一个新的局部变量**，而不是修改外层的那个。

看这个对比：

```Python
def bad_example():
    count = 0

    def counter():
        count = count + 1   # ← 想修改外层的 count
        return count

    return counter

c = bad_example()
print(c())
```

运行结果：

```Plaintext
UnboundLocalError: local variable 'count' referenced before assignment
```

报错了！Python 在 `counter` 里看到 `count = ...` 这种赋值，就认定 `count` 是 `counter` 的局部变量。可你又在赋值前就用了它（`count + 1` 右边的 `count`），于是它抱怨："你还没给这个局部变量赋值呢，就要用它？"

解决办法就是告诉 Python："我用的不是局部变量，是外面那一层的 `count`。"这就是 `nonlocal` 的作用：

```Python
def good_example():
    count = 0

    def counter():
        nonlocal count       # ← 声明：count 是外层的那个
        count = count + 1
        return count

    return counter
```

这样就正常了。

> **小提醒**：如果你只是**读取**外层变量而不修改它，是不需要 `nonlocal` 的。比如下面这样就不用加：

```Python
def make_greeter():
    greeting = '你好'
    def greeter(name):
        return greeting + ', ' + name   # 只读，不改
    return greeter
```

---

## 第五步：为什么需要闭包？没有它会怎样？

光讲定义不够，得看看它解决了什么实际问题。

### 场景一：想要一个"有记忆"的函数

假设你想做一个计数器，记录某个事情发生了多少次。

**没有闭包**，你只能用全局变量：

```Python
count = 0   # 全局变量，谁都能改

def tick():
    global count
    count = count + 1
    return count

print(tick())   # 1
print(tick())   # 2
```

能用，但问题很大：`count` 是全局的，项目里任何一段代码都能随手把它改了，你根本管不住。而且如果你想要**两个独立的计数器**，全局变量就抓瞎了——它们会互相干扰。

**用闭包**，干净利落：

```Python
def make_counter():
    count = 0
    def counter():
        nonlocal count
        count += 1
        return count
    return counter

a = make_counter()
b = make_counter()   # 另一个独立的计数器

print(a())   # 1
print(a())   # 2
print(b())   # 1  ← b 有自己的 count，跟 a 互不影响
print(a())   # 3
```

每个计数器都有自己的 `count`，互不干扰，而且外面谁也碰不到这个 `count`——它被闭包牢牢地"封"在里面了。

这就是闭包的第一大好处：**用私有变量保存状态，又不污染全局**。

### 场景二：批量制造"定制版"函数

你写网页，要根据不同语言生成不同的问候函数：

```Python
def make_greeter(greeting):
    def greeter(name):
        return greeting + ', ' + name
    return greeter

chinese = make_greeter('你好')
english = make_greeter('Hello')
japanese = make_greeter('こんにちは')

print(chinese('小明'))     # 你好, 小明
print(english('Tom'))      # Hello, Tom
print(japanese('太郎'))    # こんにちは, 太郎
```

`make_greeter` 就像一个工厂，你喂给它不同的"问候语"，它就吐出一个对应的问候函数。每个问候函数都"记住"了自己出厂时被设定的问候语。

如果没有闭包，你得为每种语言单独写一个函数，或者每次调用都把问候语当参数传进去，又啰嗦又容易写错。

### 场景三：装饰器（闭包的进阶用法）

Python 里非常常用的**装饰器**，本质就是闭包。比如这个给函数加日志的装饰器：

```Python
def log(func):
    def wrapper(*args, **kwargs):
        print(f'调用 {func.__name__}，参数：{args}')
        result = func(*args, **kwargs)
        print(f'{func.__name__} 执行完毕，返回：{result}')
        return result
    return wrapper

@log
def add(a, b):
    return a + b

print(add(3, 5))
```

运行结果：

```Plaintext
调用 add，参数：(3, 5)
add 执行完毕，返回：8
8
```

`wrapper` 里面用到了外层的 `func`，这就是闭包在悄悄工作。装饰器能用，靠的就是闭包把 `func` 封存住的能力。

---

## 第六步：闭包其实是个对象，可以"偷看"它的行李箱

Python 不会把闭包藏得严严实实，你可以用 `__closure__` 这个属性看到它封存了哪些变量：

```Python
def make_adder(n):
    def adder(x):
        return x + n
    return adder

add5 = make_adder(5)
print(add5(10))   # 15

# 偷看 add5 的行李箱里装了什么
print(add5.__closure__)            # 显示有几个被封存的变量
print(add5.__closure__[0].cell_contents)   # 取出第一个变量的值
```

运行结果：

```Plaintext
15
(<cell at 0x...: int object at 0x...>,)
5
```

`__closure__` 返回一个元组，里面每个元素是一个 cell 对象，`cell_contents` 就是它封存的那个变量的值。这里能看到 `add5` 把 `n = 5` 带走了。

这个技巧平时不常用，但调试闭包出问题时特别有用——能直接确认它到底记住了什么。

---

## 常见陷阱：闭包最容易踩的两个坑

### 坑一：循环里的延迟绑定

看这段代码，猜猜输出：

```Python
def make_funcs():
    funcs = []
    for i in [1, 2, 3]:
        def f():
            return i
        funcs.append(f)
    return funcs

for func in make_funcs():
    print(func())
```

你的直觉可能是输出 `1 2 3`，对吧？

实际输出：

```Plaintext
3
3
3
```

全是 3！很多人第一次遇到都会愣住。

原因：闭包封存的是**变量本身**，不是变量当时的值。三个 `f` 函数都引用了同一个变量 `i`，等循环结束，`i` 的值变成了 3。于是三个函数去取 `i` 的时候，拿到的都是 3。

怎么修？用**默认参数**把当时的值固定下来：

```Python
def make_funcs():
    funcs = []
    for i in [1, 2, 3]:
        def f(i=i):        # ← 把当前的 i 作为默认参数固定住
            return i
        funcs.append(f)
    return funcs

for func in make_funcs():
    print(func())
```

输出：

```Plaintext
1
2
3
```

这次对了。因为默认参数 `i=i` 在函数**定义的那一刻**就求值了，相当于给每个函数单独塞了一份当时的 `i`。

> **记住这条**：闭包记住的是变量，不是值。循环变量在循环结束后会变，要特别小心。

### 坑二：误用可变默认参数

闭包常和"保存状态"打交道，很多人会顺手写出这种代码：

```Python
def add_item(item, lst=[]):    # ← 用列表当默认参数
    lst.append(item)
    return lst

print(add_item('苹果'))   # ['苹果']
print(add_item('香蕉'))   # ['苹果', '香蕉']  ← 串味了！
```

输出：

```Plaintext
['苹果']
['苹果', '香蕉']
```

第二次调用居然把第一次的"苹果"也带出来了。这不是闭包，但和闭包踩的是同一个坑——**默认参数只在函数定义时求值一次**，之后所有调用共用同一个列表。

正确写法是用 `None` 当占位符：

```Python
def add_item(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

---

## 一个完整的实战例子：简易银行账户

把前面学的用起来，写一个"只能存取款、别人改不了余额"的账户：

```Python
def make_account(initial_balance):
    balance = initial_balance

    def deposit(amount):
        nonlocal balance
        balance += amount
        return f'存入 {amount}，余额 {balance}'

    def withdraw(amount):
        nonlocal balance
        if amount > balance:
            return '余额不足'
        balance -= amount
        return f'取出 {amount}，余额 {balance}'

    def get_balance():
        return balance

    # 把三个函数一起返回
    return {
        'deposit': deposit,
        'withdraw': withdraw,
        'balance': get_balance
    }

acc = make_account(100)
print(acc['deposit'](50))      # 存入 50，余额 150
print(acc['withdraw'](30))     # 取出 30，余额 120
print(acc['balance']())        # 120
print(acc['withdraw'](500))    # 余额不足

# 试试从外面直接改余额？
# print(balance)   # NameError，外面根本碰不到它
```

运行结果：

```Plaintext
存入 50，余额 150
取出 30，余额 120
120
余额不足
```

注意：`balance` 被牢牢锁在闭包里，外部代码既看不到也改不了，只能通过 `deposit` 和 `withdraw` 这两个"官方通道"来操作。这就是闭包实现**数据封装**的威力——不用写类，也能做到类似的效果。

---

## 小结

走完这一篇，你至少应该带走这几条：

- **闭包 = 函数 \+ 它定义时引用的外层变量**。内层函数把外层变量"打包带走"，让它们不随外层函数结束而消失。

- **三个条件**：嵌套函数、内层用到外层变量、外层把内层函数返回出去。三个都满足才是闭包。

- **`nonlocal`** 用来在内层函数里**修改**外层变量；只是读取的话不需要它。

- **闭包最大的价值**：用私有变量保存状态、批量制造定制函数、支撑装饰器机制，同时不污染全局命名空间。

- **最常踩的坑**：闭包记住的是变量本身而不是值，循环里的延迟绑定要用默认参数来规避。

下次再看到 Python 代码里函数套函数、还把内层函数 return 出来的写法，你就能一眼认出：哦，这是个闭包，它在偷偷记着什么东西呢。

恭喜你，又拿下一个概念！

