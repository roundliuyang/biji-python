# 模型层-ORM介绍及单表操作

### 单表操作之添加表记录

**方式一(推荐):**

```python
#注意日期按照这个格式来！
book_obj=models.Book.objects.create(title='Python葵花宝典',state=True,price=100,publish='苹果出版社',pub_date='2015-12-13')
#打印title...
print(book_obj.title)
```

注意create是有返回值的，create方法的返回值book_obj就是插入book表中的python葵花宝典这本书籍纪录对象

**方式二**:

```python
book_obj=Book(title="python葵花宝典",state=True,price=100,publish="苹果出版社",pub_date="2012-12-12")
book_obj.save()
```

**方式三:批量插入**

```python
book_list = []
    for i in range(10):
        bk_obj = models.Book(
            name='chao%s'%i,
            addr='北京%s'%i
        )
        book_list.append(bk_obj)

    models.Book.objects.bulk_create(book_list) #批量插入，速度快
```

**方式四:update_or_create--有就更新，没有就创建**

```python
obj,created = models.UserToken.objects.update_or_create(
    user=user, # 查找筛选条件
    defaults={ # 添加或者更新的数据
　　　　　　"token":random_str,
　　　　}
    )    
'''
说明：
1、第一个值obj为：新创建的model对象
2、第二个值created为：是否进行了新的数据的插入的操作，只是更新原始数据为false，新加入数据为true！
3、如果查询到多条数据，那么就会报错！因为源码里面用的是get方法！可以用异常处理！
'''
```



### 单表的查询之查询的API

前4个最重要，5--9比较容易，10中的三个方法比较难也非常重要！

一定要知道每个方法的返回值是什么，以及每个方法是由谁来调用的

**(1)all()**

models.Book.objects来调用，返回的是QuerySet类型的对象——django自己定义的数据类型 将查询出来的查询出来的所有对象放在列表中 在models的Book类中写了__str__方法，且__str__返回的是title的值，所以后面会打印title

```python
book_list = models.Book.objects.all()
print(book_list)
#遍历这个字典可以进行相应的取值操作
for i in book_list:
    print(i.title,i.price)
#也可以进行索引操作
print(book_list[0].pub_date)
```

**(2)first与last**

返回值不是QuerySet对象，而是model对象，等价于book_list[0] 调用者是QuerySet对象

```python
book_first = models.Book.objects.first()
print(book_first)
```

**(3)filter方法**

对应where语句 调用者objects，返回值是QuerySet对象

```python
book_list2 = models.Book.objects.filter(price=100)
print(book_list2)
```

(3-1)可以跟first或者last方法

```
book_list2_last = models.Book.objects.filter(price=100).last()
print(book_list2_last.pubdate)
```

(3-2)可以带多个多虑条件

```python
book_list2_more = models.Book.objects.filter(price=100,title='西游记')
print(book_list2_more)
```

**(4)get方法**

很像filter，但是，get方法有且只有一个查询结果是才有意义；如果有多个查询结果会报错! 返回值是一个model对象，利用objects调用!

```python
book_get = models.Book.objects.get(title='西游记')
print(book_get)
```

(5)exclude方法

排除——得到的是个QuerySet对象，由objects调用,也可以用QuerySet对象调用 models调用：

```python
book_exclude = models.Book.objects.exclude(title='西游记')
print(book_exclude)
```

QuerySet调用：

```python
ret = models.Book.objects.filter(price=11).exclude(id=2)
print(ret)
```

**(6)order_by排序**

得到的是QuerySet对象，由objects调用 (6-1)默认升序

```python
book_order_by_asc = models.Book.objects.order_by('title')
print('升序：',book_order_by_asc)
```

(6-2)降序排序

```python
book_order_by_desc = models.Book.objects.order_by('-title')
print('降序：',book_order_by_desc)
```

(6-3)也可以利用两个字段排序：——第一个字段相等的时候再用第二个字段排序

```python
book_order_by1 = models.Book.objects.order_by('title','price')
print('两个字段排序：',book_order_by1)
```

**(7)reverse反转**

可以在order_by的基础上加上reverse

```python
book_reverse = models.Book.objects.order_by('title').reverse()
print('排序反转：',book_reverse)
```

**(8)count计数**

返回int类型的数据，调用者是QuerySet

```python
count = models.Book.objects.all().count()
print('数据的总数：',(count,type(count)))
```

**(9)exists检测是否存在记录**

如果不加exists则表示取出来所有的值了，没必要取所有的值，这样效率不高； 加上exists相当于利用limit限制只取出来一条数据去判断有没有记录。

```python
ret = models.Book.objects.all().exists()
if ret:
    print('OK!有数据！')
```

**重点：(10)values(\*field)、values_list(\*field)、distinct()**

(10-1)values(*field) 查询所有书籍的名称： 得到的是一个QuerySet对象：`<QuerySet [{'title': 'Python葵花宝典'}, {'title': 'Python葵花宝典1'}, {'title': '金瓶瓶'}]>`

由QuerySet对象调用但是列表中放的不是一个个的对象了，而是一个个的字典！

```python
book_titles = models.Book.objects.all().values('title')
print('所有书籍的名称：',book_titles)
    """
     values的工作原理：
    现在将values中的参数改为为'title','price'（注意value与value_list中可以放两个参数）
    temp = []
    for obj in models.Book.objects.all()
        temp.append(
            'title':obj.title,
            'price':obj.price
        )
    return temp 
    """
```

(10-1-1)也可以利用操作字典的方法来操作结果：

```python
book_title_1_title = book_titles[1].get('title')
print('第二个书籍的名字:',book_title_1_title)   
```

(10-2)values_list(*field)方法 与value方法一样，调用者与返回值均是QuerySet对象 但是，value_list的结果是列表里面嵌套元组

```python
book_values_list = models.Book.objects.all().values_list('title')
print(book_values_list)
```

(10-3)distinct方法去重

```python
price_distinct = models.Book.objects.all().values('price').distinct()
print('price去重：',price_distinct)
```



## **元数据Meta**

**1 模型的元数据Meta**

除了抽象模型，在模型中定义的字段都会成为表中的列。如果我们需要给模型指定其他一些信息，例如排序方式、[数据库](https://cloud.tencent.com/product/tencentdb-catalog?from_column=20065&from=20065)表名等，就需要用到 Meta。Meta 是一个可选的类，具体用法如下：

```python
class Author(models.Model):
    name = models.CharField(max_length=40)
    email = models.EmailField()

    class Meta:
        managed = True
        db_table = 'author'
```

不知你是否对上述代码有影响。通过 Django 将数据库表反向生成模型时，Django 会默认带上 managed 和 db_table 信息。我主要说下 Meta 一些重要的属性，其他属性你可以通过文档信息进行学习。

**abstract**: 如果 abstract = True，模型会指定为抽象模型。它相当于面向对象编程中的抽象基类。

**proxy**：如果设置了proxy = True，表示使用代理模式的模型继承方式。

**db_table**：指定当前模型在数据库的表名。

**managed**：该属性默认值为 True，表示能创建模型和操作数据库表。

**ordering**：指定该模型生成的所有对象的排序方式。默认按升序排列，如果在字段名前加上字符**“-”**则表示按降序排列，如果使用字符问号**“？”**表示随机排列。

```javascript
ordering = ['pub_date']             # 表示按'pub_date'字段进行升序排列
ordering = ['-pub_date']            # 表示按'pub_date'字段进行降序排列
ordering = ['-pub_date', 'author']  # 表示先按'pub_date'字段进行降序排列，再按`author`字段进行升序排列。
```

**verbose_name**：给模型设置别名。如果不指定它，Django 会使用小写的模型名作为默认值。

```javascript
verbose_name = "book"
verbose_name = "图书"
```

**verbose_name_plural**：因为英语单词有单数和复数两种形式，这个属性是模型对象的复数名。中文则跟 verbose_name 值一致。如果不指定该选项，那么默认的复数名字是 verbose_name 加上**‘s’**。

```javascript
verbose_name_plural = "books"
verbose_name_plural = "图书"
```

**indexes**：为当前模型建立索引列表。用法如下：

```javascript
from django.db import models

class Customer(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)

    class Meta:
        indexes = [
            models.Index(fields=['last_name', 'first_name']),
            models.Index(fields=['first_name'], name='first_name_idx'),
        ]
```

**2 模型的继承**

根据模型的 Meta 信息设置，模型继承方式可以分为三种：

1）抽象模型

模型的 Meta 类中含有 abstract = True 属性。抽象模型一般被当作基类，它持有子类共有的字段。值得注意的是，抽象模型在数据库中不会生成表。

```python
from django.db import models

# 抽象模型
class Person(models.Model):
    name = models.CharField(max_length=500)
    age = models.PositiveIntegerField()
    
    class Meta:
        abstract = True

# 子模型
class Student(Person):
    school_name = models.CharField(max_length=20)
```

子模型如果没有定义 Meta 类，那么会继承抽象模型的 Meta 类。但是 abstract 属性不会被继承。











