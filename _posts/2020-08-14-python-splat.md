### Splat运算符

`*` 延伸运算符，splat 运算符，解包运算符

*常常与乘法运算有关，但是在Python中，则是splat运算符的两倍。

该运算符就像一个彩盒。笔者把延伸运算符——相当于splat的JavaScript——这个过程可以看做是打开一个多米诺骨牌，形成一个更大的序列，但是splat需要更有力的类比。

利用以下例子更容易解释清楚。

a = [1,2,3]
b = [*a,4,5,6]print(b) # [1,2,3,4,5,6]

在代码实例中，将a的内容移入（解包）到新列表b中。


### 如何使用*args和**kwargs

我们知道splat运算符解包多个值，并且知道函数参数有两种类型。若现在还未弄明白，那么可以将*args理解为变元的缩写，而**kwargs理解为关键字变元的缩写。

各参数用于解压其各自的变元类型，允许使用可变字长变元列表进行函数调用。例如，创建一个函数表示学生的考试分数。

```python
def printScores(student,*scores):
    print(f"Student Name:{student}")
    for score in scores:
        print(score)

printScores("Jonathan",100, 95, 88, 92, 99)
```

噢，等等，我并未称呼其为*args？是的，“args”是一个标准化规范，但仍然只是一个名称。事实上，在*args中，唯一的星号是真正的参与者，创建了列表，其内容则是来自函数调用的位置参数（在这些定义变元后）。

这些理清楚之后，**kwargs就很容易理解了。名称并不重要，重要的是双星号创建了字典，其内容是关键字参数，来自函数调用（在这些定义变元后）。

为了更好地演示，我们创建一个函数，输出参加全国英语等级考试的人员名单。

```python
def printPetNames(owner,**pets):
    print(f"Owner Name:{owner}")
    for pet,name in pets.items():
        print(f"{pet}:{name}")

printPetNames("Jonathan", dog="Brock",fish=["Larry", "Curly", "Moe"],turtle="Shelldon")

# Owner Name:Jonathan
# dog:Brock
# fish:['Larry', 'Curly', 'Moe']
# turtle:Shelldon
```

### 额外

最后，再给大家提供一些额外的“知识福利”。

下面一些谏言可有助于大家规避常见的陷阱并扩展知识面。

- 利用*args和**kwargs作为标准化规范，把握好位置参数和关键字参数；
- 不能将**kwargs置于*args前，否则将产生错误；
- 清楚意识到关键字参数和**kwargs之间的冲突，该值本是传达**kwarg的意思，但是却不明就里的成为关键字参数的名称；
- 在函数调用时可以采用splat运算符。
