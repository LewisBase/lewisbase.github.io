---
layout: post
title: 揭秘Python中的args与kwargs
subtitle: 简译与总结
date: 2019-09-13
author: lewisbase
header-img:
categories: 
    - Python
---

最近看到一篇详细介绍Python中args与kwargs关键词的文章，自己对与这两个关键词的用法很不是很熟练，在此搬运分享一下：

原文地址：[Python args and kwargs: Demystified](https://realpython.com/python-kwargs-and-args/) 

这个教程在细节上非常详尽，很适合初学者阅读。

> Python程序的函数定义中经常出现两个奇怪的参数：*args与**kwargs。这篇教程将对两者的用法进行详细的揭秘，教导你如何更灵活地使用*args与*kwargs创建自己的函数。
> 
> 通过本文你将学习到：
> * \*args与\*\*kwargs的实际意义；
> * 如何在函数定义中使用*args与**kwargs
> * 如何使用\*解包迭代器
> * 如何使用\*\*解包字典
> 
> ## 向函数传递多个参数
> 
> \*args与\*\*kwargs允许你向函数传递多个参数或者关键字参数。下面的代码是一个接收两个参数并返回它们的和的函数：
> 
>     def my_sum(a,b):
>         return a + b
> 
> 这个函数能够完美运行，但是受限于进能够接收两个参数。如果你需要对一组未知数量的数求和，难道要每次根据不同的数量来建立不同的函数吗？
> 
> ## 在函数定义中使用args变量
> 
> 对于上面的疑问，我们的回答当然是"No!"。Python提供了多种像函数传递未定数量参数的方法。人们最常用的便是将一个包含所有参数的列表或者集合传向函数。此时我们的`my_sum`函数将变为下面的形式：
> 
>     # sum_integers_list.py
>     def my_sum(my_integers):
>         result = 0
>         for x in my_integers:
>             result += x
>         return result
> 
>     list_of_integers = [1,2,3]
>     print(mu_sum(list_of_integers))
> 
> 这个方法的确可以解决问题，但你仍然需要在调用函数前建立一个列表。这将工作变得繁琐，尤其在你不知道列表中应该包含那些内容的时候。
> 
> 这将是\*args发挥作用的地方，它能够允许你传递不同数量的位置参数，请看下面的例子：
> 
>     # sum_integers_args.py
>     def my_sum(*args):
>         result = 0
>         # Iterating over the Python args tuple
>         for x in args:
>             result += x
>         return result
> 
>     print(my_sum(1,2,3))
> 
> 在这个新的例子中，你不再需要向函数传递一个列表。取而代之的是三个不同的位置参数，my_sum函数将这些参数打包入一个名为args的单一迭代对象。
> 
> 注意args其实只是一个名称，你不必非得使用这几个字母，可以用任意其他名称来替代：
> 
>     # sum_integers_args_2.py
>     def my_sum(*integers):
>         result = 0
>         for x in integers:
>             result += x
>         return result
> 
>     print(my_sum(1,2,3))
> 
> 所以说真正重要的是这里的解包操作符\*。\*这里传入的其实是一个元组而非列表，元组与列表都支持迭代和切片，但元组是不可变的。我们可以用下列代码测试一下两者的区别：
> 
>     # change_list.py
>     my_list = [1,2,3]
>     my_list[0] = 9
>     print(my_list)
> 
> 运行以后你会发现列表中的首个元素变成了9：
> 
>     $ python change_list.py
>     [9,2,3]
> 
> 而元组则不然，强行改变其值会导致错误：
> 
>     # change_tuple.py
>     my_tuple = (1,2,3)
>     my_tuple[0] = 9
>     print(my_tuple)
> 
>     $ python change_tuple.py
>     Traceback (most recent call last):
>         File "change_tuple.py", line 3, in <module>
>             my_tuple[0] = 9
>     TypeError: 'tuple' object does not support item assignment
> 
> ## 在函数定义中使用kwargs变量
> 
> 现在你了解了\*args的用法，那\*\*kwargs又是怎样的呢？两者其实即为相似，但是\*\*kwargs传递的并非位置参数而是关键字参数。示例如下：
> 
>     # concatenate.py
>     def concatenate(**kwargs):
>         result = ""
>         # Iterating over the Python kwargs dictionary
>         for arg in kwargs.values():
>             result += arg
>         return result
> 
>     print(concatenate(a="Real",b="Python",c="Is",d="Great",e="!"))
> 
> 运行上面的脚本时，`concatenate()`将遍历kwargs传入的字典并汇聚其中所有的值：
> 
>     $ python concatenate.py
>     RealPythonIsGreat!
> 
> 同args一样，kwargs也仅仅是一个名称，其关键是解包操作符\*\*。上文中的例子同样可以改写为：
> 
>     # concatenate_2.py
>     def concatenate(**words):
>         result = ""
>         for arg in words.values():
>             result += arg
>         return result
> 
>     print(concatenate(a="Real",b="Python",c="Is",d="Great",e="!"))
> 
> 注意上述例子中遍历的是一个标准字典，如果你需要像实例中一样遍历一个字典的值，不要忘了使用`.values()`。如果你忘记了使用该方法，你就会发现遍历的结果变成了字典的键：
> 
>     # concatenate_keys.py
>     def concatenate(**kwargs):
>         result = ""
>         # Iterating over the keys of the Python kwargs dictionary
>         for arg in kwargs:
>             result += arg
>         return result
> 
>     print(concatenate(a="Real",b="Python",c="Is",d="Great",e="!"))
> 
>     $ python concatenate_keys.py
>     abcde
> 
> ## 函数参数的顺序
> 
> 现在你基本了解了args和kwargs的用法，你开始准备运用新学到的知识编写一个复杂函数，但是我们该如何将这些位置参数和命名变量放置在一起呢？
> 
> 此时你就该注意函数中参数的顺序了，如同不具有默认值的参数应该放在具有默认值参数之前一样，\*args应当放置在\*\*kwargs之前才能保证函数运转正常。
> 
> 简单来说，函数中的参数顺序应当为：
> 1. 标准参数
> 2. \*args参数
> 3. \*\*kwargs参数
> 
> 正确的示例：
> 
>     # correct_function_definition.py
>     def my_function(a,b,*args,**kwargs):
>         pass
> 
> 如果我们把顺序写错了会怎样呢？亲自试错一下也许跟有助于记忆：
> 
>     # wrong_function_definition.py
>     def my_function(a,b,**kwargs,*args):
>         pass
> 
>     $ python wrong_function_definition.py
>     File "wrong_function_definition.py", line 2
>         def my_function(a,b,**kwargs,*args):
>                                      ^
>     SyntaxError: invalid syntax
> 
> ## *与**操作符的解包操作
> 
> 现在我们已经初步了解了\*与\*\*操作符在函数参数中的作用，接下来我们继续深入学习\*与\*\*的功能。
> 
> 操作符\*与\*\*在Python2中首次使用，并在[PEP 448](https://www.python.org/dev/peps/pep-0448/)的帮助下在Python3.5中变得更加强大。简而言之，解包操作符是在Python中从可迭代对象中解包值的操作符。单星号运算符*可以用于Python提供的任何迭代器，而双星号运算符**只能用于字典。
> 
>     # print_list.py
>     my_list = [1,2,3]
>     print(my_list)
> 
>     $ python print_list.py
>     [1,2,3]
> 
> 若在`print()`函数中对`my_list`用*进行解包，则变为：
> 
>     # print_unpacked_list.py
>     my_list = [1,2,3]
>     print(*my_list)
> 
>     $ python print_unpacked_list.py
>     1 2 3
> 
> 此时`print()`打印的不再是列表，而是列表中具体的值。在自己定义的函数中同样可以使用*操作符，效果同样：
> 
>     # unpacking_call.py
>     def my_sum(a,b,c):
>         print(a + b + c)
>     
>     my_list = [1,2,3]
>     my_sum(*my_list)
> 
>     $ python unpacking_call.py
>     6
> 
> 上述用法中需要保证函数的参数个数于解包对象的值个数是一致的，否则会导致语法错误。
> 
> 当使用\*操作符解包列表并将参数传递给函数时，就好像单独传递每个参数一样。这意味着可以使用多个解包操作符从多个列表中获取值，并将它们全部传递给一个函数。
> 
>     # sum integers_args_3.py
>     def my_sum(*args):
>         result = 0
>         for x in args:
>             result += x
>         return result
> 
>     list1 = [1,2,3]
>     list2 = [4,5]
>     list3 = [6,7,8,9]
> 
>     print(my_sum(*list1,*list2,*list3))
> 
>     $ python sum_integers_args_3.py
>     45
> 
> \*操作符还有更为方便的用法，比如说，你需要把一个数组分成三部分：首值，末值以及所有中间值。可以通过如下的方式实现：
> 
>     # extract_list_body.py
>     my_list = [1,2,3,4,5,6]
>     a,*b,c = my_list
>     print(a)
>     print(b)
>     print(c)
> 
>     $ python extract_list_body.py
>     1
>     [2,3,4,5]
>     6
> 
> 你甚至可以用\*操作符实现拆分后再合并的功能：
> 
>     # merging_lists.py
>     my_first_list = [1,2,3]
>     my_second_list = [4,5,6]
>     my_merged_list = [*my_first_list,*my_second_list]
>     print(my_merged_list)
> 
>     $ python merging_lists.py
>     [1,2,3,4,5,6]
> 
> 同样的操作通过\*\*操作符也可以在字典间实现：
> 
>     # merging_dict.py
>     my_first_dict = {"A":1,"B":2}
>     my_second_dict = {"C":3,"D":4}
>     my_merged_dict = {**my_first_dict,**my_second_dict}
>     print(my_merged_dict)
> 
>     $ python merging_dict.py
>     {'A':1,'B':2,'C':3,'D':4}
> 
> 这里如果使用\*操作符的话将仅对字典的键进行解包。
> 
> \*操作符还可以对字符串进行解包：
> 
>     # string_to_list.py
>     a = [*'RealPython']
>     print(a)
> 
>     $ python string_to_list.py
>     ['R', 'e', 'a', 'l', 'P', 'y', 't', 'h', 'o', 'n']
> 
> 
> 
> 