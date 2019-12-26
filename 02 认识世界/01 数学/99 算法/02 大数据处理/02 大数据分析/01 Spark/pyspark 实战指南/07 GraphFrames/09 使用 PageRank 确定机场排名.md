---
title: 09 使用 PageRank 确定机场排名
toc: true
date: 2019-07-02
---
7.9 使用 PageRank 确定机场排名
因为 GraphFrames 建立在 GraphX 之上，所以有几个算法是我们可以立即利用的。PageRank在 Google Search Engine中广泛使用，由 Larry Page创建。这里我们来引用 Wikipedia 的解释：
“PageRank的工作原理是对到连接页面的数量和质量进行计数，从而估计该页面的重要性。缺省的假定是：越是重要的网站接收到的其他网站的链接就越多。”
虽然上面的例子是关于网页的，但这一极好的理念可以用于任何图结构，无论它是来自网页、自行车站还是机场。并且 GraphFrames 的界面就像调用一个方法一样简单。GraphFrames.PageRank将把 PageRank 结果作为新的 column 追加到 DataFrame 的节点中来使我们后续的分析更加简单。
由于本数据集中包含的各机场有很多航班和连接，我们可以使用 PageRank 算法使 Spark 迭代地遍历图形，以计算出每个机场重要性的粗略估计值：
请注意，resetProbability＝0.15表示复位到随机节点的概率（这是默认值），而 maxIter＝5是设定的迭代次数。有关 PageRank 参数的更多信息，请访问 Wikipedia>PageRank（https://en.wikipedia.org/wiki/PageRank）。
PageRank的结果展现在下面的条形图中：
在机场排名方面，PageRank算法已经确定 ATL（Hartsfield-Jackson Atlanta International Airport）是美国最重要的机场。这个观察结果是有道理的，因为 ATL 不仅是美国最繁忙的机场（http://bit.ly/2eTGHs4），而且也是世界上最繁忙的机场（2000～2015）（http://bit.ly/2eTGDsy）。
