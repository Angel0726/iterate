---
title: 301 装饰器 decorator
toc: true
date: 2019-02-05
---
# 可以补充进来的

- <span style="color:red;">functools 里面到底有那些功能？还是要总结下的。</span>


# 装饰器 Decorator


现在，假设我们想对一个函数添加一些功能，比如，在函数调用前后自动打印日志，但又不希望修改函数的定义，怎么办呢？

这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。本质上，decorator 就是一个返回函数的函数。




举例：我们要定义一个能打印日志的 decorator，如下：

```py
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

说明：

- 上面的 `log` 函数，是一个 decorator，它接受一个函数作为参数，并返回一个函数。返回的这个函数里面包含传入的函数的执行和对这个传入函数的名称的打印。

## 使用装饰器

举例：

```py
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)

    return wrapper


@log
def now():
    print('2000-3-25')


def now_t():
    print('2015-3-25')


f = now
print(f)
print(f.__name__)
f()

print('\n---')
f = now_t
print(f)
print(f.__name__)
f()

print('\n---')
f=log(now_t)
print(f)
print(f.__name__)
f()

print('\n---')
now()
now_t()
```

输出：

```
<function log.<locals>.wrapper at 0x00000296A204D048>
wrapper
call now():
2000-3-25

---
<function now_t at 0x00000296A2026948>
now_t
2015-3-25

---
<function log.<locals>.wrapper at 0x00000296A2026AF8>
wrapper
call now_t():
2015-3-25

---
call now():
2000-3-25
2015-3-25
```

可见：

- 装饰器加了装饰器之后，`f=now` 的 `f` 是装饰器返回的函数，函数名字是 `wrapper`。执行 f() 的时候，执行的是 `wrapper`。
- 没有加装饰器的时候，函数 `f=now_t` 是 `now_t` ，函数名字是 `now_t。`
- 加装饰器，相当于执行 `now = log(now)`。

说明：

- 由于 `log()` 是一个 decorator，返回一个函数，所以，**原来的 `now()` 函数仍然存在，只是现在同名的 `now` 变量指向了新的函数，于是调用`now()`将执行新函数，即在`log()`函数中返回的`wrapper()`函数。**
- `wrapper()`函数的参数定义是`(*args, **kw)`，因此，`wrapper()`函数可以接受任意参数的调用。在`wrapper()`函数内，首先打印日志，再紧接着调用原始函数。

## decorator 本身需要传入参数的情况

如果 decorator 本身需要传入参数，那就需要编写一个返回 decorator 的高阶函数，写出来会更复杂。比如，要自定义 log 的文本：

```py
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator


@log('execute')
def now():
    print('2000-3-25')


def now_t():
    print('2015-3-25')


f = now
print(f)
print(f.__name__)
f()

print('\n---')
f = now_t
print(f)
print(f.__name__)
f()

print('\n---')
f=log('execute')
print(f)
print(f.__name__)
print(f(now_t))
print(f(now_t).__name__)
f(now_t)()


print('\n---')
now()
now_t()
```

输出：

```
<function log.<locals>.decorator.<locals>.wrapper at 0x000002BC9AA36AF8>
wrapper
execute now():
2000-3-25

---
<function now_t at 0x000002BC9AA5D048>
now_t
2015-3-25

---
<function log.<locals>.decorator at 0x000002BC9AA36B88>
decorator
<function log.<locals>.decorator.<locals>.wrapper at 0x000002BC9AA36C18>
wrapper
execute now_t():
2015-3-25

---
execute now():
2000-3-25
2015-3-25
```

说明：

- 我们来剖析上面的语句，首先执行`log('execute')`，返回的是 `decorator` 函数，再调用返回的函数，参数是`now`函数，返回值最终是 `wrapper` 函数。
- 和两层嵌套的 decorator 相比，3层嵌套的效果是这样的 `now = log('execute')(now)`

<span style="color:red;">嗯，不错的</span>

## 装饰器返回的函数的 `__name__` 属性

以上两种 decorator 的定义都没有问题，但还差最后一步。

因为我们讲了函数也是对象，它有`__name__`等属性，但你去看经过 decorator 装饰之后的函数，它们的`__name__`已经从原来的`'now'`变成了`'wrapper'`。

因为返回的那个`wrapper()`函数名字就是`'wrapper'`，**所以，需要把原始函数的 `__name__` 等属性复制到`wrapper()`函数中，否则，有些依赖函数签名的代码执行就会出错。**<span style="color:red;">那些事依赖函数签名的代码？</span>

那么，具体怎么做呢？

不需要编写 `wrapper.__name__ = func.__name__` 这样的代码，Python内置的 `functools.wraps` 就是干这个事的。

举例：

```py
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator


@log('execute')
def now():
    print('2000-3-25')


def now_t():
    print('2015-3-25')


f = now
print(f)
print(f.__name__)
f()

print('\n---')
f = now_t
print(f)
print(f.__name__)
f()

print('\n---')
f=log('execute')
print(f)
print(f.__name__)
print(f(now_t))
print(f(now_t).__name__)
f(now_t)()


print('\n---')
now()
now_t()
```

输出：

```
<function now at 0x00000275AF4F6AF8>
now
execute now():
2000-3-25

---
<function now_t at 0x00000275AF51D048>
now_t
2015-3-25

---
<function log.<locals>.decorator at 0x00000275AF4F6B88>
decorator
<function now_t at 0x00000275AF4F6C18>
now_t
execute now_t():
2015-3-25

---
execute now():
2000-3-25
2015-3-25
```

可见：

- `wrapper` 的名字都变回原来的名字了。

说明：

- 在 `wrapper()` 的前面加上 `@functools.wraps(func)` 即可。<span style="color:red;">嗯，不错，这个看来是必须的。</span>






# 原文及引用

- [装饰器](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014318435599930270c0381a3b44db991cd6d858064ac0000)
