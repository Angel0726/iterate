---
title: development
toc: true
date: 2019-06-30
---
26. 开发工具
************

本章中描述的各模块可帮你编写 Python 程序。例如，"pydoc" 模块接受一个模
块并根据该模块的内容来生成文档。"doctest" 和 "unittest" 这两个模块包含
了用于编写单元测试的框架，并可用于自动测试所编写的代码，验证预期的输出
是否产生。**2to3** 程序能够将 Python 2.x 源代码翻译成有效的 Python 3.x
源代码。

本章中描述的模块列表是：

* 26.1. "typing" --- 类型标注支持

  * 26.1.1. 类型别名

  * 26.1.2. NewType

  * 26.1.3. Callable

  * 26.1.4. 泛型(Generic)

  * 26.1.5. 用户定义的泛型类型

  * 26.1.6. "Any" 类型

  * 26.1.7. 类，函数和修饰器.

* 26.2. "pydoc" --- Documentation generator and online help system

* 26.3. "doctest" --- 测试交互性的 Python 示例

  * 26.3.1. Simple Usage: Checking Examples in Docstrings

  * 26.3.2. Simple Usage: Checking Examples in a Text File

  * 26.3.3. How It Works

    * 26.3.3.1. Which Docstrings Are Examined?

    * 26.3.3.2. How are Docstring Examples Recognized?

    * 26.3.3.3. What's the Execution Context?

    * 26.3.3.4. What About Exceptions?

    * 26.3.3.5. Option Flags

    * 26.3.3.6. Directives

    * 26.3.3.7. 警告

  * 26.3.4. Basic API

  * 26.3.5. Unittest API

  * 26.3.6. Advanced API

    * 26.3.6.1. DocTest Objects

    * 26.3.6.2. Example Objects

    * 26.3.6.3. DocTestFinder objects

    * 26.3.6.4. DocTestParser objects

    * 26.3.6.5. DocTestRunner objects

    * 26.3.6.6. OutputChecker objects

  * 26.3.7. 调试

  * 26.3.8. Soapbox

* 26.4. "unittest" --- 单元测试框架

  * 26.4.1. 基本实例

  * 26.4.2. 命令行界面

    * 26.4.2.1. 命令行选项

  * 26.4.3. 探索性测试

  * 26.4.4. 组织你的测试代码

  * 26.4.5. 复用已有的测试代码

  * 26.4.6. 跳过测试与预计的失败

  * 26.4.7. Distinguishing test iterations using subtests

  * 26.4.8. 类与函数

    * 26.4.8.1. 测试用例

      * 26.4.8.1.1. Deprecated aliases

    * 26.4.8.2. Grouping tests

    * 26.4.8.3. Loading and running tests

      * 26.4.8.3.1. load_tests Protocol

  * 26.4.9. Class and Module Fixtures

    * 26.4.9.1. setUpClass and tearDownClass

    * 26.4.9.2. setUpModule and tearDownModule

  * 26.4.10. Signal Handling

* 26.5. "unittest.mock" --- mock object library

  * 26.5.1. Quick Guide

  * 26.5.2. The Mock Class

    * 26.5.2.1. Calling

    * 26.5.2.2. Deleting Attributes

    * 26.5.2.3. Mock names and the name attribute

    * 26.5.2.4. Attaching Mocks as Attributes

  * 26.5.3. The patchers

    * 26.5.3.1. patch

    * 26.5.3.2. patch.object

    * 26.5.3.3. patch.dict

    * 26.5.3.4. patch.multiple

    * 26.5.3.5. patch methods: start and stop

    * 26.5.3.6. patch builtins

    * 26.5.3.7. TEST_PREFIX

    * 26.5.3.8. Nesting Patch Decorators

    * 26.5.3.9. Where to patch

    * 26.5.3.10. Patching Descriptors and Proxy Objects

  * 26.5.4. MagicMock and magic method support

    * 26.5.4.1. Mocking Magic Methods

    * 26.5.4.2. Magic Mock

  * 26.5.5. Helpers

    * 26.5.5.1. sentinel

    * 26.5.5.2. DEFAULT

    * 26.5.5.3. call

    * 26.5.5.4. create_autospec

    * 26.5.5.5. ANY

    * 26.5.5.6. FILTER_DIR

    * 26.5.5.7. mock_open

    * 26.5.5.8. Autospeccing

* 26.6. "unittest.mock" 上手指南

  * 26.6.1. 使用 mock

    * 26.6.1.1. 模拟方法调用

    * 26.6.1.2. 对象上的方法调用的 mock

    * 26.6.1.3. Mocking Classes

    * 26.6.1.4. Naming your mocks

    * 26.6.1.5. Tracking all Calls

    * 26.6.1.6. Setting Return Values and Attributes

    * 26.6.1.7. Raising exceptions with mocks

    * 26.6.1.8. Side effect functions and iterables

    * 26.6.1.9. Creating a Mock from an Existing Object

  * 26.6.2. Patch Decorators

  * 26.6.3. Further Examples

    * 26.6.3.1. Mocking chained calls

    * 26.6.3.2. Partial mocking

    * 26.6.3.3. Mocking a Generator Method

    * 26.6.3.4. Applying the same patch to every test method

    * 26.6.3.5. Mocking Unbound Methods

    * 26.6.3.6. Checking multiple calls with mock

    * 26.6.3.7. Coping with mutable arguments

    * 26.6.3.8. Nesting Patches

    * 26.6.3.9. Mocking a dictionary with MagicMock

    * 26.6.3.10. Mock subclasses and their attributes

    * 26.6.3.11. Mocking imports with patch.dict

    * 26.6.3.12. Tracking order of calls and less verbose call
      assertions

    * 26.6.3.13. More complex argument matching

* 26.7. 2to3 - 自动将 Python 2 代码转为 Python 3 代码

  * 26.7.1. 使用 2to3

  * 26.7.2. 修复器

  * 26.7.3. "lib2to3" —— 2to3 支持库

* 26.8. "test" --- Regression tests package for Python

  * 26.8.1. Writing Unit Tests for the "test" package

  * 26.8.2. Running tests using the command-line interface

* 26.9. "test.support" --- Utilities for the Python test suite
