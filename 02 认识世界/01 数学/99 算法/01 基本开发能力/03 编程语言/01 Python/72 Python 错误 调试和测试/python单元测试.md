---
title: python单元测试
toc: true
date: 2018-06-11 08:14:29
---
# Python单元测试


## 缘由：


一直对单元测试心有疑虑，因为工作中并没有使用，但是又觉得一直不是很清楚，想弄清楚，因此要总结下，先从 Python 的单元测试总结


## 要点：




### 1.unittest的使用


**需要补充**


### 2.普通的例子：


unittest自己会去看，通过反射机制，看下哪些类是从 TestCase 继承的，然后将里面的所有 test_开头的方法都执行一遍，而且在执行的时候会看有没有 setUp 和 tearDown 函数，有的话会对应执行。

代码如下：


    # unittest一般是对于类进行测试还是对于一个 py 文件写对应的 test.py文件？
    import unittest


    class MyDict(dict):
        pass


    # setUp和 tearDown 在每次执行测试用例的时候都会用的，测试用例即 test_开头的函数
    # 但是 setUp 和 tearDown 里面到底应该放什么内容？
    class TestMyDict(unittest.TestCase):
        def setUp(self):
            print('测试前准备')

        def tearDown(self):
            print('测试后清理')

        def test_init(self):
            print('test_init')
            md = MyDict(one=1, two=2)
            self.assertEqual(md['one'], 1)
            self.assertEqual(md['two'], 2)
            # self.assertEqual(md['two'], 3)

        def test_nothing(self):
            print('test_nothing')
            pass


    if __name__ == '__main__':
        unittest.main()


输出：


    测试前准备
    test_init
    测试后清理
    测试前准备
    test_nothing
    测试后清理
    ..
    ----------------------------------------------------------------------
    Ran 2 tests in 0.000s
    OK


注：**这个例子感觉比较简单，有没有比较复杂的例子，而且在实际的工作中的应用的话是什么样子的？而且还有几个问题：unittest一般是对于类进行测试还是对于一个 py 文件写对应的 test.py文件？setUp和 tearDown 里面到底应该放什么内容？**


### 3.unittest的命令行的使用方式




    # Python test_module.py
    # Python -m unittest test_module
    # Python -m unittest test_module.test_class
    # Python -m unittest test_module.test_class.test_method


注：**感觉讲的不是很全面，要补充**


## COMMENT：
