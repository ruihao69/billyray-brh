---
title: Python Tricks!
tags: Programming, Python, Tricks
---

These are the python tricks that I have learned from other ppls scripts or tutorials. Long-term updating and source is provided.

# Vannila Python

## Looping with your list

`do ... while`, `for` loops often rely on a **index** to monitor a loop, and apart from that, one can use a python *list object* to perform looping. Here is a rather simple example. 

```python
In [1]: list1 = ['item1', 'item2', 'item3']

In [2]: for item in list1:
   ...:     print(item)
   ...: 
item1
item2
item3
```

Inappropariately, we call the above loop a **list** loop. The one we learnt first is called an **index** loop.

### Using `enumerate`

`enumerate` is useful for obtaining an indexed list:

```python
    (0, seq[0]), (1, seq[1]), (2, seq[2]), ...
```

`enumerate` really makes us take advantage of both **index loop** and **list loop**, here is such an example:

```python
In [6]: for index, item in enumerate(list1):
   ...:     print("indix:" + str(index) + ",\t" + "value:" + item)
   ...: 
indix:0,	value:item1
indix:1,	value:item2
indix:2,	value:item3
```

### Using zip to loop more than 1 list

```python
 >>> list(zip('abcdefg', range(3), range(4)))
   [('a', 0, 0), ('b', 1, 1), ('c', 2, 2)]

The zip object yields n-length tuples, where n is the number of iterables passed as positional arguments to zip().  The i-th element in every tuple comes from the i-th iterable argument to zip().  This continues until the shortest argument is exhausted.
```

Here is an example:

```python
In [8]: list1 = ['item1-1', 'item1-2', 'item1-3']
   ...: list2 = ['item2-1', 'item2-2', 'item2-3']
   ...: 

In [9]: for item1, item2 in zip(list1, list2):
   ...:     print("list1: " + item1 + ",\t" + "list2: " + item2)
   ...: 
list1: item1-1,	list2: item2-1
list1: item1-2,	list2: item2-2
list1: item1-3,	list2: item2-3
```

### Using `for` and `if` in a list to generate list

You can condense your code so that you can generate a list using **loop in a list**, rather than using `for` loop with `append` method.

Looping in a list means something like

```python
x = [command for item in list101]
```

Here is an example for that

```python
In [1]: list1 = ["item1-1", "item1-2", "item1-3"]
In [3]: x = [item + "-1" for item in list1 if not item.endswith("1")]
             ----------   ------------------------------------------
             The command                 The for loop
In [4]: x
Out[4]: ['item1-2-1', 'item1-3-1']
```

Note that in this way, there is no ":"s or indentation needed anywhere, just simply `[<the command> + <for loop>]` in a list (escaped by \[ \]).



**Source**:

[python数组循环处理](https://www.huuinn.com/archives/429)

[runoob.com, Python enumerate() 函数](https://www.runoob.com/python/python-func-enumerate.html)



