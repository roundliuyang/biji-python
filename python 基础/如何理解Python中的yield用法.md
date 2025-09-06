# 如何理解Python中的yield用法?



相信你已经不止一次在函数中看到关键词`yield`，它起着什么作用？返回什么？和`return`又有着什么区别呢？这篇文章将会揭开`yield`的神秘面纱，并给出最浅显易懂的例子。

## **`yield`关键字做了什么？**

如果不太好理解`yield`，可以先把`yield`当作`return`的同胞兄弟来看，他们都在函数中使用，并履行着返回某种结果的职责。

这两者的区别是：

有`return`的函数直接返回所有结果，程序终止不再运行，并销毁局部变量；

![img](如何理解Python中的yield用法.assets/v2-c174dafa27d729a4c8a9f2f081f2eb82_1440w.jpg)



而有`yield`的函数则返回一个可迭代的 generator（[生成器](https://zhida.zhihu.com/search?content_id=147820930&content_type=Article&match_order=1&q=生成器&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTY5OTg3NzYsInEiOiLnlJ_miJDlmagiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxNDc4MjA5MzAsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.XQw59KipsLYNEdDRA6c4t3sFsVxcsu1kxrqfWdfq4Uc&zhida_source=entity)）对象，你可以使用for循环或者调用next()方法遍历生成器对象来提取结果。

什么是生成器呢？在 Python 中，使用了`yield`的函数被称为生成器。有点套娃的感觉，但事实就是这样，调用一个`yield`函数，就会返回一个生成器对象。

![img](如何理解Python中的yield用法.assets/v2-b9ca5799b878a9a25bac12da5054aa6c_1440w.jpg)



在调用生成器函数的过程中，每次遇到 `yield` 时函数会暂停并保存当前所有的运行信息（保留局部变量），返回`yield`的值, 并在下一次执行`next()`方法时从当前位置继续运行，直到生成器被全部遍历完。

![img](如何理解Python中的yield用法.assets/v2-0d177d233ccd166850c3bd94a1d02e3b_1440w.jpg)

先看一个简单的例子：

![img](如何理解Python中的yield用法.assets/v2-3646248fa65317725855a096a6d675c0_1440w.jpg)



上面函数返回一个生成器，现在对该生成器进行遍历：

![img](如何理解Python中的yield用法.assets/v2-eaba76320daf1c3f190cf6d106ce1d6a_1440w.jpg)



或者调用next()方法：

![img](如何理解Python中的yield用法.assets/v2-40e831fd0aae61dcfbe209c46f500f58_1440w.jpg)



可以看到，simple_generator函数返回一个生成器，调用`next()`方法后，函数开始运行，遇到第一个`yield`关键字，返回生成的值（1），程序暂停；

第二次调用`next()`方法，代码从上次暂停的位置开始执行，并遇到了第二个`yield`关键字，再返回生成的值（2），程序暂停；

第三次调用也是如此，返回生成的值（3），生成器耗尽，程序终止；

到这里你可能就明白`yield`和`return`的关系和区别了，带`yield`的函数是一个生成器，这个生成器有一个方法就是next，next就相当于“下一步”生成哪个数，这一次的next开始的地方是接着上一次的next停止的地方执行的。

所以调用next的时候，生成器并不会从函数的开始执行，只是接着上一步停止的地方开始，然后遇到`yield`后，return出要生成的数，此步就结束。

有个经典的例子就是使用yield生成[斐波那契数列](https://zhida.zhihu.com/search?content_id=147820930&content_type=Article&match_order=1&q=斐波那契数列&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTY5OTg3NzYsInEiOiLmlpDms6LpgqPlpZHmlbDliJciLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxNDc4MjA5MzAsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.A6EBt6D7jLs4quIq049oAAK0PZPO3IcOUJUomN-15ss&zhida_source=entity)：

```python3
def fab(max): 
    n, a, b = 0, 0, 1 
    while n < max: 
        yield b      # 使用 yield
        # print b 
        a, b = b, a + b 
        n = n + 1
 
for n in fab(5): 
    print n
```

## **生成器有哪些作用？**

如果想具体化数据的形式，通常会将数据存储在一个列表中。但这样做，列表的内容将占用有形内存。列表越大，占用的内存资源就越多。

但是，如果数据集有某种逻辑，就不必存储在一个列表中，只需编写一个生成器，它将在需要时生成这些值，基本不占用内存。

