---
title: NLP 实践教程
toc: true
date: 2019-11-17
---
# NLP 实践教程



在研究和处理自然语言处理的很多问题时，除了关注各种各样基础的数据，高级的深度学习模型、算法外，其实中间还涉及了很多处理技术，比如：词干提取、词形还原、句法分析、语义分析等，虽然不同的语言特征不同，但是这其中大部分步骤都是存在于大多数NLP领域任务中的。今天特别为大家准备了一篇包含NLP重要技术概念学习和实践的文章，希望无论是基础数据、技术理论还是代码实践大家都可以在这里学习和成长。

而本文正是对上面提到的技术撰写的理论+实践的指南教程，大家不仅对NLP 技术理论可以有更多更深入的了解，对每项技术常用的技术工具与实践也会有更全面的了解。本文是上篇，接下来我们还会继续完善下篇，大家可以持续关注。

# **▌前言**

文本、图像和视频这样的非结构数据包含着非常丰富的信息。然而，由于在处理和分析数据时的内在复杂性，人们往往不愿花费额外的时间和精力从结构化数据集中冒险分析这些可能是一个潜在的金矿的非结构化数据源。

自然语言处理（NLP）就是利用工具、技术和算法来处理和理解基于自然语言的数据，这些数据通常是非结构化的，如文本、语音等。在本系列文章中，我们将着眼于从业者和数据科学家可以利用的经过验证和测试的策略、技术和工作流程，从中提取有用的见解。我们还将介绍一些有用的和有趣的 NLP 用例，如何处理和理解文本数据，并提供教程和实践示例。

# **▌概要**

此系列内容的本质是理论概念的综合介绍，但重点将会放在各种 NLP 问题的实践技术和策略上。你会了解到如何开始分析文本语料库中的语法和语义。

本系列文章中涉及的一些主要技术包括：

1.文本处理与文本理解

2.特征工程和文本表示

3.文本数据的监督学习模型

4.文本数据的无监督学习模型

5.高级的主题

本系列文章将通过案例实践详细介绍 NLP 的以下内容：

1.数据检索与网页抓取

2.文本清理与预处理

3.语言标记

4.浅解析

5.选区和依赖分析

6.命名实体识别

7.情绪与情感分析

# **▌入门**

在这个教程中，我们将构建一个端到端教程，从 web 上获取一些文本数据并在此基础上展示示例！研究的源数据是从 inshorts 获取的新闻文章，inshorts 为我们提供各种话题的 60 字简短新闻。

在本文中，我们将使用技术、体育和世界新闻类别的新闻文本数据。接下来会为大家介绍如何从他们的网站上爬取和检索这些新闻文章的一些基本知识。

# **▌标准NLP工作流程**

假设大家知道 crispm - dm 模型，它通常是执行任何数据科学项目的行业标准。通常，任何基于nlp的问题都可以通过具有一系列步骤的有方法的工作流来解决。主要步骤如下图所示。

![img](https://file.ai100.com.cn/files/sogou-articles/original/ef5e40a4-3b54-477a-ac3e-cb18b486aa7d/640.png)

我们通常从文本文档的语料库开始，遵循文本清理、预处理、解析和基本的探索性数据分析的这一标准过程。通常我们使用相关的特性工程技术来表示文本。根据要解决的问题，构建监督预测模型或非监督模型，通常更关注模式挖掘和分组。最后，我们评估模型和与客户的成功的标准，并部署最终模型以供将来使用。

# **▌数据检索爬取新闻文章**

我们通过使用 python 检索新闻文章来爬取 inshorts 网页。专注于技术、体育和世界新闻的文章，我们将为每个类别检索一页的文章。下图描述了一个典型的新闻类别页面，还突出显示了每篇文章文本内容的 HTML 部分。

![img](https://file.ai100.com.cn/files/sogou-articles/original/ce394b64-a2ad-484f-8290-eb97dbc52344/640.png)



因此，我们可以在上面提到的页面中看到包含每个新闻文章文本内容的特定 HTML 标记。利用 BeautifulSoup 和 requests 库提取新闻文章的这些信息。

首先加载以下依赖项：

```
import requests
from bs4 import BeautifulSoup
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import os
%matplotlib inline
```

现在，开始构建一个函数，该函数利用 requests 访问并获取三个新闻类别的每个页面的 HTML 内容。然后，使用 BeautifulSoup 解析和提取每个类别的所有新闻标题和文本内容。通过访问特定的 HTML 标记和类所在的位置来查找内容。

```
seed_urls = ['https://inshorts.com/en/read/technology',
             'https://inshorts.com/en/read/sports',
             'https://inshorts.com/en/read/world']

def build_dataset(seed_urls):
    news_data = []
    for url in seed_urls:
        news_category = url.split('/')[-1]
        data = requests.get(url)
        soup = BeautifulSoup(data.content, 'html.parser')

        news_articles = [{'news_headline': headline.find('span',
                                                         attrs={"itemprop": "headline"}).string,
                          'news_article': article.find('div',
                                                       attrs={"itemprop": "articleBody"}).string,
                          'news_category': news_category}

                            for headline, article in
                             zip(soup.find_all('div',
                                               class_=["news-card-title news-right-box"]),
                                 soup.find_all('div',
                                               class_=["news-card-content news-right-box"]))
                        ]
        news_data.extend(news_articles)

    df =  pd.DataFrame(news_data)
    df = df[['news_headline', 'news_article', 'news_category']]
    return df
```

很明显，我们提取新闻标题、文本和类别，并构建一个数据框架，其中每一行对应于特定的新闻文章。现在我们将调用这个函数并构建我们的数据集。

```
news_df = build_dataset(seed_urls)
news_df.head(10)
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/cd940f56-6b41-4f7c-b21f-9f7ed46c589b/640.png)



现在有了一个格式统一的新闻文章数据集，可以使用以下代码快速检查新闻文章的总数。

```
news_df.news_category.value_counts()
Output:
-------
world         25
sports        25
technology    24
Name: news_category, dtype: int64
```



#

###### ▌文本清理 & 预处理

清理和预处理文本数据通常涉及多个步骤。在这里，将重点介绍一些在自然语言处理（NLP）中大量使用的最重要的步骤。我们将利用 nltk 和 spacy 这两个在 NLP 中最先进的库。通常pip 安装 <library> 或 conda 安装 <library> 就足够了。如果遇到加载 spacy 语言模型的问题，请按照下面显示的步骤来解决这个问题（我曾经在我的一个系统中遇到过这个问题）。

```
# OPTIONAL: ONLY USE IF SPACY FAILS TO LOAD LANGUAGE MODEL
# Use the following command to install spaCy
> pip install -U spacy
OR
> conda install -c conda-forge spacy
# Download the following language model and store it in disk
https://github.com/explosion/spacy-models/releases/tag/en_core_web_md-2.0.0
# Link the same to spacy
> python -m spacy link ./spacymodels/en_core_web_md-2.0.0/en_core_web_md en_core
Linking successful
    ./spacymodels/en_core_web_md-2.0.0/en_core_web_md --> ./Anaconda3/lib/site-packages/spacy/data/en_core
You can now load the model via spacy.load('en_core')
```

现在加载文本预处理所需的依赖项。我们会把否定词从停止词中去掉，因为在情感分析期间可能会有用处，因此在这里我们对其进行了保留。

```
import spacy
import pandas as pd
import numpy as np
import nltk
from nltk.tokenize.toktok import ToktokTokenizer
import re
from bs4 import BeautifulSoup
from contractions import CONTRACTION_MAP
import unicodedata
nlp = spacy.load('en_core', parse=True, tag=True, entity=True)
#nlp_vec = spacy.load('en_vecs', parse = True, tag=True, #entity=True)
tokenizer = ToktokTokenizer()
stopword_list = nltk.corpus.stopwords.words('english')
stopword_list.remove('no')
stopword_list.remove('not')
```



## **▌删除 HTML 标签**

通常非结构化文本包含很多噪音，特别是使用 web 或屏幕爬取等技术而获得的数据。HTML 标记就是这些其中一种典型的噪音，它们对理解和分析文本并没有太大的价值。

```
def strip_html_tags(text):
    soup = BeautifulSoup(text, "html.parser")
    stripped_text = soup.get_text()
    return stripped_text

strip_html_tags('<html><h2>Some important text</h2></html>')
```



```
'Some important text'
```



很明显，从上面的输出中，我们可以删除不必要的 HTML 标记，并从任何一个文档中保留有用文本信息。

## **▌删除重音字符**

通常在任何文本语料库中，都可能要处理重音字符或字母，尤其是只想分析英语语言时。因此，我们需要确保这些字符被转换并标准化为 ASCII 字符。下面是一个转换  é to e  的简单例子。



```
def remove_accented_chars(text):
    text = unicodedata.normalize('NFKD', text).encode('ascii', 'ignore').decode('utf-8', 'ignore')
    return text

remove_accented_chars('Sómě Áccěntěd těxt')
```



```
'Some Accented text'
```

此函数展示了如何方便地将重音字符转换为正常的英文字符，从而有助于规范语料库中的单词。



## **▌扩大收缩**

缩写是单词或音节的缩写形式。它们经常存在于英语的书面语言或口语中。这些词的缩短版本或收缩是通过去除特定的字母和声音而产生的。将每一个缩写转换为展开的原始形式有助于文本标准化。我们利用库中 contractions.py 文件里一套标准的可获得的收缩形式。



```
def expand_contractions(text, contraction_mapping=CONTRACTION_MAP):

    contractions_pattern = re.compile('({})'.format('|'.join(contraction_mapping.keys())),
                                      flags=re.IGNORECASE|re.DOTALL)
    def expand_match(contraction):
        match = contraction.group(0)
        first_char = match[0]
        expanded_contraction = contraction_mapping.get(match)\
                                if contraction_mapping.get(match)\
                                else contraction_mapping.get(match.lower())
        expanded_contraction = first_char+expanded_contraction[1:]
        return expanded_contraction

    expanded_text = contractions_pattern.sub(expand_match, text)
    expanded_text = re.sub("'", "", expanded_text)
    return expanded_text

expand_contractions("Y'all can't expand contractions I'd think")
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/ff770fa2-40fe-4344-b9b0-242e0493864e/640.png)

可以看到函数是如何帮助从前面的输出扩展收缩的。是否存在更好的方法？当然！如果我们有足够的例子，我们甚至可以训练一个深度学习模型来获得更好的性能。

## **▌删除特殊字符**

特殊字符和符号通常是非字母数字字符，有时甚至是数字字符，这增加了非结构化文本中的额外噪声。通常，可以使用简单的正则表达式删除它们。



```
def remove_special_characters(text, remove_digits=False):
    pattern = r'[^a-zA-z0-9\s]' if not remove_digits else r'[^a-zA-z\s]'
    text = re.sub(pattern, '', text)
    return text

remove_special_characters("Well this was fun! What do you think? 123#@!",
                          remove_digits=True)
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/641bbe48-4094-4d59-bc52-a1f3b698fa3f/640.png)

# **▌词干提取**

要理解词干提取，需要对词干表示的是什么有一些了解。词干也被称为单词的基本形式，我们可以通过添加词缀的方式来创造一个新词，这个过程称为变形。考虑“jump”这个词。你可以给它添加词缀，形成新的单词，比如 jumps， jumped， 和 jumping。在这种情况下，基本的单词 “jump” 就是词干。

![img](https://file.ai100.com.cn/files/sogou-articles/original/5c5d9e4a-2f58-41a6-9e83-5d1f64fa2f8d/640.png)

图中显示了所有的变形中词干是如何呈现的，它形成了每个变形都是基于使用词缀构建的基础。从词形变化的形式中获得基本形式和根词干的反向过程称为词干提取。词干提取有助于我们对词干进行标准化，而不考虑词其变形，这有助于许多应用，如文本的分类和聚类，甚至应用在信息检索中。接下来为大家介绍现在流行的 Porter stemmer。



```
def simple_stemmer(text):
    ps = nltk.porter.PorterStemmer()
    text = ' '.join([ps.stem(word) for word in text.split()])
    return text

simple_stemmer("My system keeps crashing his crashed yesterday, ours crashes daily")
```

![img](https://file.ai100.com.cn/files/sogou-articles/original/92c83b11-5bd3-4d0c-9fda-6220a0036b3d/640.png)



Porter stemmer 算法得名于它的发明 Martin Porter 博士。最初，据说该算法总共有 5 个不同的阶段来减少对其词干的影响，每个阶段都有自己的一套规则。

这里有一点需要注意，通常词干有一组固定的规则，因此，词根可能不和字典进行匹配。也就是说，词干的语义可能不是正确的，并且可能没有出现在字典中（从前面的输出中可以看到例子）。

## **▌词形还原**

词形还原与词干提取非常相似，我们去掉词缀以获得单词的基本形式。然而，这种情况下的基本形式被称为词根，而不是根词干。不同之处在于，词根始终是字典上一个正确的词（存在于字典中），但根词干可能不是这样。因此，词根，也被称为词元，永远出现在字典中。nltk 和spacy 都有很好的词形还原工具。这里使用 spacy。



```
def lemmatize_text(text):
    text = nlp(text)
    text = ' '.join([word.lemma_ if word.lemma_ != '-PRON-' else word.text for word in text])
    return text

lemmatize_text("My system keeps crashing! his crashed yesterday, ours crashes daily")=
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/4beb5ac1-7d67-4490-81bb-80924fe72851/640.png)

> 可以看到单词的语义不受此影响，而我们的文本仍然是标准化的。
>
> 需要注意的是，词形还原过程比词干提取要慢得多，因为除了通过删除词缀形成词根或词元的过程外还需要确定词元是否存在于字典中这一步骤。

## **▌删除停用词**

那些没有或几乎没有意义的词，尤其是在从文本构建有意义的特征时，被称为停用词或停止词。如果你在语料库中统计一个简单的术语或词的频率，这类词通常频率最高。典型的，这些可以是冠词，连词，介词等等。停用词的一些例子如 a, an, the，等等。



```
def remove_stopwords(text, is_lower_case=False):
    tokens = tokenizer.tokenize(text)
    tokens = [token.strip() for token in tokens]
    if is_lower_case:
        filtered_tokens = [token for token in tokens if token not in stopword_list]
    else:
        filtered_tokens = [token for token in tokens if token.lower() not in stopword_list]
    filtered_text = ' '.join(filtered_tokens)
    return filtered_text

remove_stopwords("The, and, if are stopwords, computer is not")
```

![img](https://file.ai100.com.cn/files/sogou-articles/original/e59de7a2-2a3c-4bd2-8f2f-27c209e97f9a/640.png)

没有通用的停止词列表，但是我们使用 nltk 中的标准停止词列表。还可以根据需要添加特定领域的停止词。

# **▌整合——构建文本标准化器**

当然我们可以继续使用更多的技术，如纠正拼写、语法等，但现在将把上面所学的一切结合在一起，并将这些操作链接起来，构建一个文本规范化器来对文本数据进行预处理。



```
def normalize_corpus(corpus, html_stripping=True, contraction_expansion=True,
                     accented_char_removal=True, text_lower_case=True,
                     text_lemmatization=True, special_char_removal=True,
                     stopword_removal=True, remove_digits=True):

    normalized_corpus = []
    # normalize each document in the corpus
    for doc in corpus:
        # strip HTML
        if html_stripping:
            doc = strip_html_tags(doc)
        # remove accented characters
        if accented_char_removal:
            doc = remove_accented_chars(doc)
        # expand contractions
        if contraction_expansion:
            doc = expand_contractions(doc)
        # lowercase the text
        if text_lower_case:
            doc = doc.lower()
        # remove extra newlines
        doc = re.sub(r'[\r|\n|\r\n]+', ' ',doc)
        # lemmatize text
        if text_lemmatization:
            doc = lemmatize_text(doc)
        # remove special characters and\or digits
        if special_char_removal:
            # insert spaces between special characters to isolate them
            special_char_pattern = re.compile(r'([{.(-)!}])')
            doc = special_char_pattern.sub(" \\1 ", doc)
            doc = remove_special_characters(doc, remove_digits=remove_digits)
        # remove extra whitespace
        doc = re.sub(' +', ' ', doc)
        # remove stopwords
        if stopword_removal:
            doc = remove_stopwords(doc, is_lower_case=text_lower_case)

        normalized_corpus.append(doc)

    return normalized_corpus
```

现在开始对这个函数实际应用一下。首先将每条新闻的新闻标题和新闻文章文本合并在一起形成一个文档。然后，我们对它们进行预处理。



```
# combining headline and article text
news_df['full_text'] = news_df["news_headline"].map(str)+ '. ' + news_df["news_article"]

# pre-process text and store the same
news_df['clean_text'] = normalize_corpus(news_df['full_text'])
norm_corpus = list(news_df['clean_text'])

# show a sample news article
news_df.iloc[1][['full_text', 'clean_text']].to_dict()
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/bd7a7a27-eca3-4c3e-88f4-e7597756da1c/640.png)

到这可以看到我们的文本预处理器如何帮助对我们新闻文章进行预处理，在此之后，如果需要可以将该数据集保存到磁盘中，以便以后经常加载以供将来分析。

![img](https://file.ai100.com.cn/files/sogou-articles/original/57b5707f-4a86-480c-b561-3f95b931c08f/640.png)

# **▌理解语法与结构**

对于任何一种语言来说，语法和结构通常都是密切相关的，在这其中，一套特定的规则、惯例和法则控制着单词和短语的组合方式；短语合并成子句；子句被组合成句子。我们将特别讨论演示的示例中英语语法和结构。在英语中，通常单词结合在一起形成其他组成成分。这些成分包括单词、短语、从句和句子。例如考虑一下这个句子，“The brown fox is quick and he is jumping over the lazy dog”,它是由一串单词组成的，只是单词本身并没有告诉我们很多信息。

![img](https://file.ai100.com.cn/files/sogou-articles/original/f1f838c7-4a0f-446d-9eec-6873b7961bd5/640.png)

了解语言的结构和语法有助于文本处理、标注和解析等领域的后续操作，如文本分类或摘要。下面为大家介绍理解文本语法的典型解析技术。

- Parts of Speech (POS) Tagging

- Shallow Parsing or Chunking

- Constituency Parsing

- Dependency Parsing

我们将在后面的章节中讨论这些技术。如果我们使用基本的 POS 标记，对前面的例句 “The brown fox is quick and he is jumping over The lazy dog” 进行注释，就会看到如下图所示。

![img](https://file.ai100.com.cn/files/sogou-articles/original/d645565a-7aab-4616-9507-e3acbf68c6fa/640.png)

因此，一个句子通常遵循以下组成部分的层次结构：句子→子句→短语→单词

# **▌词性标记**

词类（POS）是根据上下文的语法和角色给词划分到特定的词类范畴。通常，词汇可以分为以下几个主要类别。

- **N（oun）**：这通常用来描述某些物体或实体的词，例如狐狸、狗、书等。 POS 标记名词为符号 N。

- **V（erb）**：动词是用来描述某些行为、状态或事件的词。还有各种各样的子范畴，如助动词、反身动词和及物动词（还有更多）。一些典型的动词例子是跑、跳、读和写的。 动词的POS标记符号为 V。

- **Adj（ective）**: 形容词是用来描述或限定其他词的词，通常是名词和名词短语。“美丽的花”这个短语有名词“花”，这个名词用形容词 “美丽的” 来描述或限定。形容词的词性标记符号是　ADJ。

- **Adv（erb）**： 副词通常作为其他词的修饰词，包括名词、形容词、动词或其他副词。短语very beautiful flower 的副词是 very，修饰形容词 beautiful，表示花的美丽程度。副词的词尾标记是 ADV。

除了这四种主要的词类之外，英语中还有其他经常出现的词类。它们包括代词、介词、感叹词、连词、限定词等。此外，像名词（N）这样的每个 POS 标签还可以进一步细分为单数名词（NN）、单数专有名词（NNP）和复数名词（NNS）等类别。

对词进行分类和标记 POS 标签的过程称为词性标记或 POS 标注。POS 标注用于注释单词和描述单词的 POS，这对于进行特定分析非常有帮助，比如缩小名词范围，看看哪些是最突出的，消除歧义和语法分析。我们将利用 nltk 和 spacy  ，它们通常使用 Penn Treebank notation 进行 POS 标记。

![img](https://file.ai100.com.cn/files/sogou-articles/original/4079c20e-d416-4b59-a3e8-8b15749cfa5b/640.png)



可以看到，每个库都以自己的方式处理令牌，并为它们分配特定的标记。根据我们所看到的，spacy 似乎比 nltk 做得稍好一些。

# **▌浅解析或分块**

根据我们前面描述的层次结构，一组词组成短语。而短语包含五大类：

- **名词短语（NP）**：此类短语是名词充当头词的短语。名词短语作为动词的主语或宾语。

- **动词短语（VP）**：此类短语是有一个动词充当头词。通常，动词短语有两种形式。有一种形式是既有动词成分，也有名词、形容词或副词等作为宾语的一部分。

- **形容词短语（ADJP）**：这类短语以形容词为前置词。它们的主要作用是描述或限定一个句子中的名词和代词，它们将被放在名词或代词之前或之后。

- **副词短语（ADVP）**：这类短语起类似像副词的作用，因为副词在短语中作为头词。副词短语用作名词、动词或副词的修饰词，它提供了描述或限定它们的更多细节。

- **介词短语（PP）**：这些短语通常包含介词作为前置词和其他词汇成分，如名词、代词等。这些行为就像形容词或副词，用来描述其他的词或短语。



浅解析，也称为轻解析或分块，是一种流行的自然语言处理技术，它分析一个句子的结构，将其分解为最小的组成部分（如单词），并将它们组合成更高层次的短语。这包括 POS标注和句子中的短语。

![img](https://file.ai100.com.cn/files/sogou-articles/original/bf92f600-1cba-4788-b6e8-e66187146be0/640.png)



我们将利用 conll2000 语料库来训练我们的浅解析器模型。这个语料库在 nltk 中可获得块注释，并且我们将使用大约 10K 条记录来训练我们的模型。一个带注释的句子示例如下所示。



```
from nltk.corpus import conll2000

data = conll2000.chunked_sents()
train_data = data[:10900]
test_data = data[10900:]

print(len(train_data), len(test_data))
print(train_data[1])
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/fdec84f7-417b-4061-8a82-9da35945df47/640.png)

在前面的输出中，可以看到我们的数据是已经用短语和 POS 标记元数据注释的语句，这将有助于培训我们的浅层解析器模型。我们将利用两个分块实用函数 tree2conlltags，为每个令牌获取单词、词类标记和短语标记的三元组，并使用 conlltags2tree 从这些令牌三元组生成解析树。我们将使用这些函数来训练我们的解析器。下面是一个示例。



```
from nltk.chunk.util import tree2conlltags, conlltags2tree

wtc = tree2conlltags(train_data[1])
wtc﻿
```

![img](https://file.ai100.com.cn/files/sogou-articles/original/d735f3d0-8743-46d7-b3ff-3f3d524b74cc/640.png)



短语标记使用 IOB 格式。这个符号表示内部、外部和开始。标记前的 B 前缀表示它是短语的开始，I 前缀表示它在短语内。O 标记表示该标签不属于任何短语。当后面跟着的是同类型之间不存在O 标记时，后续标记一直使用 B 标记。

我们将定义一个函数 conll_tag_ chunk() 来从带有短语注释的句子中提取 POS 和短语标记，并且名为 combined_taggers() 的函数来训练带有值标记的多样标记。



```
def conll_tag_chunks(chunk_sents):
    tagged_sents = [tree2conlltags(tree) for tree in chunk_sents]
    return [[(t, c) for (w, t, c) in sent] for sent in tagged_sents]


def combined_tagger(train_data, taggers, backoff=None):
    for tagger in taggers:
        backoff = tagger(train_data, backoff=backoff)
    return backoff
```

现在我们要再定义一个类 NGramTagChunker，它将把标记的句子作为训练输入，获取他们的WTC三元组 （词、POS 标记、短语标记），并将一个具有 UnigramTagger 的 BigramTagger 作为 BackOff Tagger。 我们还将定义一个 parse() 函数来对新句执行浅层解析。

> UnigramTagger , BigramTagger 和 TrigramTagger 是继承于基类 NGramTagger 的类，它本身继承自从 SequentialBackoffTagger 类继承的 context ttagger 类。

我们将使用这个类对 conll2000 分块 train_data 进行训练，并在 test_data 上评估模型性能。



```
from nltk.tag import UnigramTagger, BigramTagger
from nltk.chunk import ChunkParserI

# define the chunker class
class NGramTagChunker(ChunkParserI):

  def __init__(self, train_sentences,
               tagger_classes=[UnigramTagger, BigramTagger]):
    train_sent_tags = conll_tag_chunks(train_sentences)
    self.chunk_tagger = combined_tagger(train_sent_tags, tagger_classes)

  def parse(self, tagged_sentence):
    if not tagged_sentence:
        return None
    pos_tags = [tag for word, tag in tagged_sentence]
    chunk_pos_tags = self.chunk_tagger.tag(pos_tags)
    chunk_tags = [chunk_tag for (pos_tag, chunk_tag) in chunk_pos_tags]
    wpc_tags = [(word, pos_tag, chunk_tag) for ((word, pos_tag), chunk_tag)
                     in zip(tagged_sentence, chunk_tags)]
    return conlltags2tree(wpc_tags)

# train chunker model
ntc = NGramTagChunker(train_data)

# evaluate chunker model performance
print(ntc.evaluate(test_data))
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/4ae3a71c-6c5d-41a3-a0a1-fea9883d0c69/640.png)

我们的分块模型准确率在90%左右，相当不错！现在，让我们利用这个模型对我们之前使用的新闻标题 “US unveils world’s most powerful supercomputer, beats China” 进行分块解析。

![img](https://file.ai100.com.cn/files/sogou-articles/original/74272caf-0874-438a-905b-acf8cead5a6d/640.png)



你可以看到已经在新闻文章中找到了两个名词短语（NP）和一个动词短语（VP）。每个单词的 POS 标记都是可见的。我们也可以用树的形式来表示。如果 nltk 抛出错误，您可能需要安装 ghostscript 。



```
from IPython.display import display

## download and install ghostscript from https://www.ghostscript.com/download/gsdnld.html

# often need to add to the path manually (for windows)
os.environ['PATH'] = os.environ['PATH']+";C:\\Program Files\\gs\\gs9.09\\bin\\"

display(chunk_tree)
```



![img](https://file.ai100.com.cn/files/sogou-articles/original/3d39b7e4-4363-4886-ab4a-ac52130385b7/640.png)



在对新闻标题进行浅层解析后，之前的输出提供了良好的结构感。

到这里我们主要从词和短语两个结构的技术概念讲解及一些基础工具的介绍，后续我们还会为大家讲解子句及句子层级结构上的讲解以及更多的实践教程，大家可以继续关注人工智能头条带来的精彩内容。

> 原文链接：<https://towardsdatascience.com/a-practitioners-guide-to-natural-language-processing-part-i-processing-understanding-text-9f4abfd13e72>


# 相关

- [关于NLP你还不会却必须要学会的事儿—NLP实践教程指南第一篇](https://www.tinymind.cn/articles/711)
