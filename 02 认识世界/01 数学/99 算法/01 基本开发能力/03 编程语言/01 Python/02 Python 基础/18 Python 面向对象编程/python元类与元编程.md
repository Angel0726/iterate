---
title: python元类与元编程
toc: true
date: 2018-06-11 08:14:29
---
# Python元类与元编程


## 要点：


这个在 ORM 框架中经常会用到。数据库里面，各种框架的代码都支持 ORM


## 要点：




### 1.先说明如何通过 type 来动态创建一个类：




    # java等是在编译的时候确定的，Python是在运行的时候定义的，
    # Python可以用 class 来定义一个类，但是也可以使用 type 函数来动态的创建这个类
    # Python在类里面动态的维护了一张 hash 表，比如说我添加一个属性或者函数，它只需要在 hash 表中进行添加即可。

    # 假如说这个类有一个构造函数和一个 say_hello的方法
    def init(self, name):
        self.name = name


    def say_hello(self):
        print('Hello, %s!' % self.name)


    # 动态的生成一个类
    # WARINING (object,)这个地方需要传递一个基类的列表，
    # 如果不加逗号，Python会认为这个是一个元素，而不是一个元组，因此要显示的标注一个逗号！
    Hello = type('Hello',  # 'Hello'是它的名字
                 (object,),  # object是他的基类
                 {'__init__': init, 'hello': say_hello})  # 有哪些方法和属性，这些方法和属性都是之前已经声明的一些函数

    '''
    class Hello:
        def __init__(...)
        def hello(...)
    '''

    h = Hello('Tom')
    h.hello()


输出：


    Hello, Tom!




### 2.使用 metaClass 即元类来建立一个有 add 功能的 list




    # medaClass 最大的作用是控制类的创建过程。控制体现在修改传递进来的参数
    # 比如给 list 添加一个 add 函数，之前可以继承，然后添加，
    # 现在是用元类做，用元类的__new__函数，在构造这个类的时候，就将这个属性添加进去了

    def add(self, value):
        self.append(value)


    # metaClass即元类一定要从 type 继承
    class ListMetaclass(type):
        def __new__(cls, name, bases, attrs):
            print(cls)  # 传进来的类
            print(name)  # 传进来的名字
            print(bases)  # 基类
            print(type(bases))  # 只读的，不能直接修改 但是基本不会去修改
            print(type(attrs))  # 这个是一张 hash 表，有什么额外的东西，都可以放到这张 hash 表中带进去
            # 可以用一个 lambda 函数来写，
            # attrs['add'] = lambda self, value: self.append(value) #为什么 self 能指到 list 呢？
            # 也可以写在外面，然后这里设定一下
            attrs['add'] = add
            attrs['name'] = 'Tom'
            return type.__new__(cls, name, bases, attrs)  # 调用 type.__new__去真实的创建这个对象


    # metaClass 的作用就是控制类的创建
    class MyList(list, metaclass=ListMetaclass):  # 额外增加 add 方法，实际等价于 append。
        pass


    mli = MyList()
    mli.add(1)
    mli.add(2)
    mli.add(3)
    print(mli.name)
    print(mli)


输出：


    <class '__main__.ListMetaclass'>
    MyList
    (<class 'list'>,)
    <class 'tuple'>
    <class 'dict'>
    Tom
    [1, 2, 3]




### 3.数据库 ORM 中的使用方式


代码如下：


    # 这个最好要自己实现一遍

    # 为什么说数据库对应的列是 field 呢？
    class Field:
        def __init__(self, name, col_type):
            self.name = name
            self.col_type = col_type


    class IntegerField(Field):
        def __init__(self, name):
            # 调用父类的方法，super的两个参数，一个是当前类的名字，还有一个就是 self
            super(IntegerField, self).__init__(name, 'integer')


    class StringField(Field):
        def __init__(self, name):
            super(StringField, self).__init__(name, 'varchar(1024)')


    # 因为 User 的类属性是最先创建的，因此__new__里面才会可以从 attrs 中把 id 和 name 拿出来
    class ModelMetaclass(type):
        #除了 User 的类属性的创建之外，就轮到这个__new__被调用了
        def __new__(cls, name, bases, attrs):
            if name == 'Model':
                return type.__new__(cls, name, bases, attrs)
            print('ModelMetaClass __new__')
            print('Model name: %s' % name)
            # 将数据库的所有的列的信息放到 mapping 里面。
            mappings = {}
            for k, v in attrs.items():
                if isinstance(v, Field):
                    print('Field name: %s' % k)
                    mappings[k] = v
            for k in mappings.keys():
                attrs.pop(k)
            attrs['__mappings__'] = mappings  # 这个不是系统属性，只是为了与自己的普通的变量防止冲突，因此加上两个__
            attrs['__table__'] = name  # 数据库里面表的名字往往与类的名字相同，在将数据写道数据库中的时候会用到
            return type.__new__(cls, name, bases, attrs)


    # 为什么要从字典继承过来呢？
    class Model(dict, metaclass=ModelMetaclass):
        def __init__(self, **kvs):  # 传进一个字典结构 这个没有被调用到？看来如果子类不明确调用的话，相同的函数不会被自动调用的
            print('Model __init__')
            super(Model, self).__init__(**kvs)

        # 比如说我读 id 的时候就会去 getattr
        def __getattr__(self, key):
            try:
                return self[key]
            except KeyError:
                raise AttributeError("'Model' object has no attribute '%s'." % key)

        # 这里虽然 setattr，但是却不是把 kv 放到 attrs 里面，而是放到自己作为 dict 的子类本身就有的字典里面
        def __setattr__(self, key, value):
            print('__setattr__')
            self[key] = value

        # 只是把 sql 语句的参数生成
        def save(self):
            fields = []
            params = []
            args = []
            for k, v in self.__mappings__.items():
                fields.append(v.name)  # 把列的名字拿出来
                params.append('?')  # 用问号代替参数，这时一种标准的防止 sql 注入攻击的写法，为什么？
                args.append(getattr(self, k, None))  # 厉害，空值的话返回 None，
            # 厉害，这句拼接，行云流水
            sql = 'insert into %s(%s) values(%s)' % (self.__table__, ','.join(fields), ','.join(params))
            print('sql:', sql)
            print('args:', args)


    # 通过 create id和 create name和 User __init__ 这三局打印出来的顺序可知道：
    # 类的类属性是在最开始被初始化的，而 init 函数里面的实例属性要等到父类的初始化完成后才开始
    # 因此 id 和 name 是最先初始化的，
    # 这就解释了为什么 ModelMetaClass 的__new__里面可以对 id 和 name 从 attrs 里面去除放到自己的 mappings 里面去
    # 也因为如此，设定成 self.id和 self.name是不行的，因为要到自己的__init__执行的时候才有
    class User(Model):
        print('create id')
        id = IntegerField('id')
        print('create name')
        name = StringField('name')

        def __init__(self):
            print('User __init__')
            pass


    # 厉害，是这样的，初始的时候，我们把本来在 attrs 里面的跟数据库相关的信息通过__new__函数放到放到__mappings__里面，
    # key是 id，name   value是对应的 Field 类型的对象
    # 然后这时候自己的 attrs 里面只有__mappings__和__table__，
    # 但是因为 Model 是从 dict 继承的，而且还实现了__setattr__和__getattr__，因此，
    # 后面的 u.id 和 u.name 跟之前的 User 类里面的东西一点关系都没有，而是相当于新添加了一些 attr，但是这些 attr 也没有添加到 attrs 里面，
    # 而是放到了自己本身作为 dict 的子类因此具有的字典里面。。这个是不是？不确定？
    u = User()
    u.id = 100
    u.name = 'Tom'
    u.aaa = 1  # 这也就是为什么这个还是可以写的原因。。
    u.save()


输出：


    create id
    create name
    ModelMetaClass __new__
    Model name: User
    Field name: id
    Field name: name
    User __init__
    __setattr__
    __setattr__
    __setattr__
    sql: insert into User(id,name) values(?,?)
    args: [100, 'Tom']


这个例子还是有些厉害的，之前用 ORM 只觉得好用，但是到底是怎么实现的一直不知道，现在才知道，但是这里面还是有几个问题不清楚：是不是子类如果不明确调用的话，父类的__init__就不会被调用到？为什么 params.append('?')的问号可以防止 sql 注入攻击？Model为什么要从 dict 继承，感觉不是特别有必要把，自己的 attrs 不能使用吗？而且 attrs 里面现在是不是只有__mappings__和__table__？


## COMMENT：


感觉理解还不够深入，再看下
