# 一文读懂Python async/await异步编程

>[一文读懂Python async/await异步编程](https://zhuanlan.zhihu.com/p/698683843)

常见的Python代码都是一行一行执行的，非常易懂。然而，有时候我们也会在Python代码中看到一些`async/await`等与异步编程相关的代码。为了能够顺利读懂这些代码，我们需要了解Python异步编程的一些基础知识。

事实上，[Python 3.5](https://zhida.zhihu.com/search?content_id=243393276&content_type=Article&match_order=1&q=Python+3.5&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTcwMDAwMzcsInEiOiJQeXRob24gMy41IiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjQzMzkzMjc2LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ._zp-zweN70rO8F8MmlFRzMQbjcgHP_XiC34lFt9W45E&zhida_source=entity)就已经开始支持异步编程语法了。从这个角度来看，了解异步编程也是必要的，它早已成为了Python生态里的一部分。

Python中的异步编程的核心语法就是`async/await`两个关键字，主要涉及的概念就是协程（coroutine）。关于协程的解释，[什么是协程？](https://zhuanlan.zhihu.com/p/172471249)这篇文章给出了很好的介绍。简单来说，协程就是在一个线程（thread）里通过[事件循环](https://zhida.zhihu.com/search?content_id=243393276&content_type=Article&match_order=1&q=事件循环&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTcwMDAwMzcsInEiOiLkuovku7blvqrnjq8iLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNDMzOTMyNzYsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.92mm5MWGlnF8EhMWmtm0q7Gr-SQOwtniX3suJ-WXdbo&zhida_source=entity)（[event loop](https://zhida.zhihu.com/search?content_id=243393276&content_type=Article&match_order=1&q=event+loop&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTcwMDAwMzcsInEiOiJldmVudCBsb29wIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjQzMzkzMjc2LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.GwT-poeXOsZn2pHg3hT04y8ULdBQNhwnRDPku6Rofi8&zhida_source=entity)）模拟出多个线程并发的效果。

## Python中的协程概念

在Python中，协程coroutine有两层含义：

1. 使用`async def`定义的函数是一个coroutine，这个函数内部可以用`await`关键字。
2. 使用`async def`定义的函数，调用之后返回的值，是一个coroutine对象，可以被用于`await`或者`asyncio.run`等

我们可以看到：

1. 第一层含义是语法层面的概念，一个函数（一段代码）由`async def`定义，那么它就是一个coroutine。带来的效果是，这个函数内部可以用`await`。那么反过来就是说，一个普通的`def`定义的函数，内部不能用`await`，否则就会触发语法错误（SyntaxError）。
2. 第二层含义是Python解释器运行时的概念，`coroutine`是Python解释器里内置的一个类。当我们调用`async def`定义的函数时，得到的返回值的类型就是`coroutine`。

例如下面的代码：

```text
import asyncio

async def hello_world():
    await asyncio.sleep(1)
    print("Hello, world!")

coro = hello_world()
print(hello_world) # <function hello_world at 0x102a93e20>
print(coro.__class__) # <class 'coroutine'>
asyncio.run(coro) # Hello, world!
```

从语法层面上来说，`hello_world`函数是个coroutine函数。但是运行时，`hello_world`函数的类型依然是`function`，这个函数调用之后的返回对象`coro`是一个`coroutine`对象。

.......