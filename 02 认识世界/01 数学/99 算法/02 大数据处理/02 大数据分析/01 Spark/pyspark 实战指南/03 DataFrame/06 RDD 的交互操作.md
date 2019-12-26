---
title: 06 RDD 的交互操作
toc: true
date: 2019-07-02
---
3.6 RDD的交互操作
有两种从 RDD 变换到 DataFrame（或者 Dataset[T]）的不同方法：使用反射推断模式或以编程方式指定模式。前者可以让你写出更简洁的代码（如果你的 Spark 应用程序已经识别该模式），而在列和 DataFrame 的数据类型是在运行时才发现的情况下，后者则允许你构建 DataFrame。注意，反射是参照模式反射（schema reflection）而不是 python reflection。
3.6.1 使用反射来推断模式
在建立 DataFrame 和运行查询的过程中，我们略过了 DataFrame 的模式是自动定义的这一事实。最初，行对象通过传递一列键/值对作为行类的**kwargs来构造。然后，Spark SQL将行对象的 RDD 转变为一个 DataFrame，在 DataFrame 中键就是列，数据类型通过采样数据来推断。**kwargs结构允许你在运行时传递一个可变的参数个数给一个方法。
回到代码部分，在开始创建 swimmersJSON DataFrame之后，没有指定模式，你会注意到利用 printSchema（）方法的模式定义。
输出如下：
但是，在这个例子中，因为我们知道 id 实际上是一个 long 类型而不是 string 类型，那么要是我们想指定模式，又会怎么样呢？
3.6.2 编程指定模式
在这种情况下，我们通过在 Spark SQL中引入数据类型（pyspark.sql.types），以编程方式来指定模式，并生成一些.csv数据，如下例所示：
首先，我们根据以下的[schema]变量将模式编码成一个字符串。然后我们会利用 StructType 和 StructField 定义模式。
注意，StructField被分解为以下方面：
·name：该字段的名字
·dataType：该字段的数据类型
·nullable：指示此字段的值是否为空
最后，我们会应用我们曾为 stringCSVRDD RDD（即生成的.csv数据）创建的模式（schema），并且还会创建一个临时视图，因此我们可以使用 SQL 来查询。
有了这个例子，我们对模式便有了更精细的控制，并且可以明确知道 id 是 long 类型（上一节中 id 是一个 string 类型）：
输出如下：在许多情况下，我们可以推断模式（如上一节所示）并且和之前的例子一样，你不需要指定模式。
