---
title: 用Python构建NLP Pipeline 从思路到具体代码
toc: true
date: 2019-11-17
---
阅读时长：全文大约 2000 字，读完可能需要下面这首歌的时间

![img](https://y.gtimg.cn/music/photo_new/T002R90x90M0000026rzHh4a8xjN.jpg)Thorne RoomBuckethead - Project Little Man![img](https://res.wx.qq.com/mmbizwap/zh_CN/htmledition/images/icon/appmsg/qqmusic/icon_qqmusic_source42f400.png)

授人以鱼不如授人以渔，今天的文章由作者**Adam Geitgey授权在人工智能头条翻译发布。**不仅给出了具体代码，还一步步详细解析了实现原理和思路。正所谓有了思路，无论是做英语、汉语的语言处理，才算的上有了指导意义。

Adam Geitgey毕业于佐治亚理工学院，曾在团购网站Groupon担任软件工程师总监。目前是软件工程和机器学习顾问，课程作者，Linkedin Learning的合作讲师。

## 计算机是如何理解人类语言的?

![img](https://mmbiz.qpic.cn/mmbiz_gif/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLk93Gxof3z5Zia3YoiamlWicr21hr86sUn27eaYrYnh4gH8cYwa5TGxyHw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

让机器理解人类语言，是一件非常困难的事情。计算机的专长在处理结构化数据，但人类语言是非常复杂的，碎片化，松散，甚至不合逻辑、心口不一。

既然直男不能明白为什么女朋友会生气，那计算机当然无法理解A叫B为孙子的时候，是在喊亲戚、骂街，或只是朋友间的玩笑。

面对人类，计算机相当于是金刚陨石直男。

正是由于人工智能技术的发展，不断让我们相信，计算机总有一天可以听懂人类表达，甚至像真人一样和人沟通。那么，就让我们开始这算美好的教程吧。

------

## 创建一个NLP Pipeline

> London is the capital and most populous city of England and the United Kingdom. Standing on the River Thames in the south east of the island of Great Britain, London has been a major settlement for two millennia. It was founded by the Romans, who named it Londinium.
>
> 伦敦，是英国的首都，人口居全国之首。位于大不列颠岛东南方泰晤士河流域，在此后两个世纪内为这一地区最重要的定居点之一。它于公元50年由罗马人建立，取名为伦蒂尼恩。
>
> -- 维基百科

### Step 1：断句（句子切分）

上面介绍伦敦的一段话，可以切分成3个句子：

1. 伦敦是大不列颠的首都，人口居全国之首（London is the capital and most populous city of England and the United Kingdom）
2. 位于泰晤士河流域（Standing on the River Thames in the south east of the island of Great Britain, London has been a major settlement for two millennia）
3. 它于公元50年由罗马人建立，取名为伦蒂尼恩（It was founded by the Romans, who named it Londinium）

### Step 2：分词

由于中文的分词逻辑和英文有所不同，所以这里就直接使用原文了。接下来我们一句一句的处理。首先第一句：

> “London”, “is”, “ the”, “capital”, “and”, “most”, “populous”, “city”, “of”, “England”, “and”, “the”, “United”, “Kingdom”, “.”

英文的分词相对简单一些，两个空格之间可以看做一个词（word），标点符号也有含义，所以把标点符号也看做一个词。

**Step 3：区分单词的角色**

我们需要区分出一个词在句子中的角色，是名词？动词？还是介词。我们使用一个预先经过几百万英文句子训练、被调教好的词性标注（POS: Part Of Speech）分类模型：

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLx5gFNfVoDjcHHGVwZGzDRC10w1MPqujFgXcphy35bNibbibRI8HlW8TA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**这里有一点一定要记住**：模型只是基于统计结果给词打上标签，它并不了解一个词的真实含义，这一点和人类对词语的理解方式是完全不同的。

处理结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLzMrjDaiaFDicd6ZdibLicWmSxy2bfV01nMBjWslshX6IRUcBFkNhWqBTSQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到。我们等到的信息中，名词有两个，分别是**伦敦**和**首都**。伦敦是个独特的名称，首都是个通用的称谓，因此我们就可以判断，这句话很可能是在围绕**伦敦**这个词说事儿。

**Step 4： 文本词形还原**

很多基于字母拼写的语言，像英语、法语、德语等，都会有一些词形的变化，比如单复数变化、时态变化等。比如：

1. I had a pony（我有过一匹矮马）
2. I have two ponies （我有两匹矮马）



其实两个句子的关键点都是**矮马pony**。Ponies和pony、had和have只是同一个词的不同词形，计算机因为并不知道其中的含义，所以在它眼里都是完全不一样的东西，

让计算机明白这个道理的过程，就叫做词形还原。对之前有关伦敦介绍的第一句话进行词形还原后，得到下图

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lL7QD0Jyw9c0530zVpDt51BNWNOmZucxQ0cgIpia6qNFoHGicdVP1hUArA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Step 5：识别停用词**

> **停用词**：在信息检索中，为节省存储空间和提高搜索效率，在处理自然语言数据（或文本）之前或之后会自动过滤掉某些字或词，这些字或词即被称为Stop Words(停用词)。这些停用词都是人工输入、非自动化生成的，生成后的停用词会形成一个停用词表。但是，并没有一个明确的停用词表能够适用于所有的工具。甚至有一些工具是明确地避免使用停用词来支持短语搜索的。
>
> -- 维基百科

还是来看第一句话：

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLLjsOaY3xxfMvUgNoaS1icdvMsfKXvYQc5UvT4PmQKvFDBYWgjictqXKQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中灰色的字，仅仅是起到衔接和辅助表述的作用。他们的存在，对计算机来说更多是噪音。所以我们需要把这些词识别出来。

正如维基所说，现在虽然停用词列表很多，但一定要根据实际情况进行配置。比如英语的**the**，通常情况是停用词，但很多乐队名字里有**the**这个词，The Doors, The Who，甚至有个乐队直接就叫The The！这个时候就不能看做是停用词了。

**Step 6：解析依赖关系**

解析句子中每个词之间的依赖关系，最终建立起一个关系依赖树。这个数的root是关键动词，从这个关键动词开始，把整个句子中的词都联系起来。

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLT8yyKMF7x47LefrsD1ot9FWKG5e5vUW1zd1GJdicqlClsGkecacJictw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从这个关系树来看，主语是London，它和capital被be联系起来。然后计算机就知道，London is a capital。如此类推，我们的计算机就被训练的掌握越来越多的信息。

但因为人类语言的歧义性，这个模型依然无法适应所有场景。但是随着我们给他更多的训练，我们的NLP模型会不断提高准确性。Demo地址

```
https://explosion.ai/demos/displacy?utm_source=AiHl0
```

我们还可以选择把相关的词进行合并分组，例如把名词以及修饰它的形容词合并成一个词组短语。不过这一步工作不是必须要有的，视具体情况而定。

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLCBDONfChuJYEUujMzauQYS27SqHaS6QsTgWenMiaJg5CTUe0Xm35Hsw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**Step 7：命名实体识别**

经过以上的工作，接下来我们就可以直接使用现有的命名实体识别（NER: Named Entity Recognition）系统，来给名词打标签。比如我们可以把第一句话当中的地理名称识别出来:

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLymYPoAyLSVaK16vJgok9dONSEluTdxdw3ibjWC9oFxd63bWttlUibqaw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

大家也可以通过下面的链接，在线体验一下。随便复制粘贴一段英文，他会自动识别出里面包含哪些类别的名词：

```
https://explosion.ai/demos/displacy-ent?utm_source=AiHl0
```

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLzGjlaJoTaCoctXpTWXfb0EDCsHN85X3S5hbJvGn0icqgKDDMcFNiaI2A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Step 8：共指消解

人类的语言很复杂，但在使用过程中却是倾向于简化和省略的。比如他，它，这个，那个，前者，后者…这种指代的词，再比如缩写简称，北京大学通常称为北大，中华人民共和国通常就叫中国。这种现象，被称为共指现象。

在特定语境下人类可以毫不费力的区别出它这个字，到底指的是牛，还是手机。但是计算机需要通过共指消解才能知道下面这句话

> 它于公元50年由罗马人建立，取名为伦蒂尼恩

中的它，指的是伦敦，而不是罗马，不是罗纹，更不是萝卜。

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLlRBITr491sCqnic4yQYe2UffFx6HkluS5WnicbKULicGH0xfMVqle06bQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

共指消解相对而言是我们此次创建NLP Pipeline所有环节中，最难的部分。

## Coding

好了。思路终于讲完了。接下来就是Coding的部分。首先我们理一下思路

![img](https://mmbiz.qpic.cn/mmbiz_png/ptp8P184xjyrVuxPcv8HcclzibGMgj6lLibtPn1IJwXibZGCB8iczTDM0kxT4S2VeLfic1oObssPT6uLDG6ko4sr5dg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

提示：上述步骤只是标准流程，实际工作中需要根据项目具体的需求和条件，合理安排顺序。

### 安装spaCy

我们默认你已经安装了Python 3。如果没有的话，你知道该怎么做。接下来是安装spaCy：

```py
# Install spaCy
pip3 install -U spacy

# Download the large English model for spaCy
python3 -m spacy download en_core_web_lg

# Install textacy which will also be useful
pip3 install -U textacy
```

安装好以后，使用下面代码

```py
import spacy

# Load the large English NLP model
nlp = spacy.load('en_core_web_lg')

# The text we want to examine
text = """London is the capital and most populous city of England and
the United Kingdom.  Standing on the River Thames in the south east
of the island of Great Britain, London has been a major settlement
for two millennia. It was founded by the Romans, who named it Londinium.
"""

# Parse the text with spaCy. This runs the entire pipeline.
doc = nlp(text)

# 'doc' now contains a parsed version of text. We can use it to do anything we want!
# For example, this will print out all the named entities that were detected:
for entity in doc.ents:
    print(f"{entity.text} ({entity.label_})")
```

结果如下

```
London (GPE)
England (GPE)
the United Kingdom (GPE)
the River Thames (FAC)
Great Britain (GPE)
London (GPE)
two millennia (DATE)
Romans (NORP)
Londinium (PERSON)
```

1. GPE：地理位置、地名
2. FAC：设施、建筑
3. DATE：日期
4. NORP：国家、地区
5. PERSON：人名

我们看到，因为Londinium这个地名不够常见，所以spaCy就做了一个大胆的猜测，猜这可能是个人名。

我们接下来进一步，构建一个数据清理器。假设你拿到了一份全国酒店入住人员登记表，你想把里面的人名找出来替换掉，而不改动酒店名、地名等名词，可以这样做：

```py
import spacy

# Load the large English NLP model
nlp = spacy.load('en_core_web_lg')

# Replace a token with "REDACTED" if it is a name
def replace_name_with_placeholder(token):
    if token.ent_iob != 0 and token.ent_type_ == "PERSON":
        return "[REDACTED] "
    else:
        return token.string

# Loop through all the entities in a document and check if they are names
def scrub(text):
    doc = nlp(text)
    for ent in doc.ents:
        ent.merge()
    tokens = map(replace_name_with_placeholder, doc)
    return "".join(tokens)

s = """
In 1950, Alan Turing published his famous article "Computing Machinery and Intelligence". In 1957, Noam Chomsky’s
Syntactic Structures revolutionized Linguistics with 'universal grammar', a rule based system of syntactic structures.
"""

print(scrub(s))
```

把所有标注为[PERSON]的词都替换成REDACTED。最终结果

```txt
In 1950, [REDACTED] published his famous article "Computing Machinery and Intelligence". In 1957, [REDACTED]
Syntactic Structures revolutionized Linguistics with 'universal grammar', a rule based system of syntactic structures.
```

## 提取详细信息

利用spaCy识别并定位的名词，然后利用textacy就可以把一整篇文章的信息都提取出来。我们在wiki上复制整篇介绍伦敦的内容到以下代码

```py
import spacy
import textacy.extract

# Load the large English NLP model
nlp = spacy.load('en_core_web_lg')

# The text we want to examine
text = """London is the capital and most populous city of England and  the United Kingdom.
Standing on the River Thames in the south east of the island of Great Britain,
London has been a major settlement  for two millennia.  It was founded by the Romans,
who named it Londinium.
"""

# Parse the document with spaCy
doc = nlp(text)

# Extract semi-structured statements
statements = textacy.extract.semistructured_statements(doc, "London")

# Print the results
print("Here are the things I know about London:")

for statement in statements:
    subject, verb, fact = statement
    print(f" - {fact}")
```


你会得到如下结果

```txt
Here are the things I know about London:
 - the capital and most populous city of England and the United Kingdom
 - a major settlement for two millennia
 - the world's most populous city from around 1831 to 1925
 - beyond all comparison the largest town in England
 - still very compact
 - the world's largest city from about 1831 to 1925
 - the seat of the Government of the United Kingdom
 - vulnerable to flooding
 - "one of the World's Greenest Cities" with more than 40 percent green space or open water
 - the most populous city and metropolitan area of the European Union and the second most populous in Europe
 - the 19th largest city and the 18th largest metropolitan region in the world
 - Christian, and has a large number of churches, particularly in the City of London
 - also home to sizeable Muslim, Hindu, Sikh, and Jewish communities
 - also home to 42 Hindu temples
 - the world's most expensive office market for the last three years according to world property journal (2015) report
 - one of the pre-eminent financial centres of the world as the most important location for international finance
 - the world top city destination as ranked by TripAdvisor users
 - a major international air transport hub with the busiest city airspace in the world
 - the centre of the National Rail network, with 70 percent of rail journeys starting or ending in London
 - a major global centre of higher education teaching and research and has the largest concentration of higher education institutes in Europe
 - home to designers Vivienne Westwood, Galliano, Stella McCartney, Manolo Blahnik, and Jimmy Choo, among others
 - the setting for many works of literature
 - a major centre for television production, with studios including BBC Television Centre, The Fountain Studios and The London Studios
 - also a centre for urban music
 - the "greenest city" in Europe with 35,000 acres of public parks, woodlands and gardens
 - not the capital of England, as England does not have its own government
```

我们获得了这么多有用的信息，就可以应用在很多场景下。比如，搜索结果的相关推荐：

<center>

![mark](http://images.iterate.site/blog/image/20191104/LkJE7Dve57KU.png?imageslim)

</center>


我们可以通过下面这种方法实现上图的效果

```py
import spacy
import textacy.extract

# Load the large English NLP model
nlp = spacy.load('en_core_web_lg')

# The text we want to examine
text = """London is [.. shortened for space ..]"""

# Parse the document with spaCy
doc = nlp(text)

# Extract noun chunks that appear
noun_chunks = textacy.extract.noun_chunks(doc, min_freq=3)

# Convert noun chunks to lowercase strings
noun_chunks = map(str, noun_chunks)
noun_chunks = map(str.lower, noun_chunks)

# Print out any nouns that are at least 2 words long
for noun_chunk in set(noun_chunks):
    if len(noun_chunk.split(" ")) > 1:
        print(noun_chunk)
```


# 相关

- [用Python构建NLP Pipeline，从思路到具体代码，这篇文章一次性都讲到了](https://mp.weixin.qq.com/s?__biz=MzAwNDI4ODcxNA==&mid=2652249776&idx=1&sn=90976d13b3bdd6b3a947094fac819679&chksm=80cc8715b7bb0e03eb58f5a10ed128ad76247a89947ca183262a9b11946a2a42a2a48d64de99&mpshare=1&scene=1&srcid=0808BS1Cudvzki43NOPJ080v#rd)
- http://t.cn/RgCITGj?utm_source=AiHl0
