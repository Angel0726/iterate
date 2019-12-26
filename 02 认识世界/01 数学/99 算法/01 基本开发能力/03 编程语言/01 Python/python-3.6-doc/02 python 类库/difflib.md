---
title: difflib
toc: true
date: 2019-06-30
---
6.3. "difflib" --- 计算差异的辅助工具
*************************************

**源代码:** Lib/difflib.py

======================================================================

此模块提供用于比较序列的类和函数。 例如，它可以用于比较文件，并可以产
生各种格式的不同信息，包括 HTML 和上下文以及统一格式的差异点。 有关目
录和文件的比较，请参见 "filecmp" 模块。

class difflib.SequenceMatcher

   这是一个灵活的类，可用于比较任何类型的序列对，只要序列元素为
   *hashable* 对象。 其基本算法要早于由 Ratcliff 和 Obershelp 于 1980
   年代末期发表并以“格式塔模式匹配”的夸张名称命名的算法，并且更加有趣
   一些。 其思路是找到不包含“垃圾”元素的最长连续匹配子序列；所谓“垃圾”
   元素是指其在某种意义上没有价值，例如空白行或空白符。 （处理垃圾元素
   是对 Ratcliff 和 Obershelp 算法的一个扩展。） 然后同样的思路将递归
   地应用于匹配序列的左右序列片段。 这并不能产生最小编辑序列，但确实能
   产生在人们看来“正确”的匹配。

   **耗时:** 基本 Ratcliff-Obershelp 算法在最坏情况下为立方时间而在一
   般情况下为平方时间。 "SequenceMatcher" 在最坏情况下为平方时间而在一
   般情况下的行为受到序列中有多少相同元素这一因素的微妙影响；在最佳情
   况下则为线性时间。

   **自动垃圾启发式计算:** "SequenceMatcher" 支持使用启发式计算来自动
   将特定序列项视为垃圾。 这种启发式计算会统计每个单独项在序列中出现的
   次数。 如果某一项（在第一项之后）的重复次数超过序列长度的 1% 并且序
   列长度至少有 200 项，该项会被标记为“热门”并被视为序列匹配中的垃圾。
   这种启发式计算可以通过在创建 "SequenceMatcher" 时将 "autojunk" 参数
   设为 "False" 来关闭。

   3.2 新版功能: *autojunk* 形参。

class difflib.Differ

   这个类的作用是比较由文本行组成的序列，并产生可供人阅读的差异或增量
   信息。 Differ 统一使用 "SequenceMatcher" 来完成行序列的比较以及相似
   （接近匹配）行内部字符序列的比较。

   "Differ" 增量的每一行均以双字母代码打头：

   +------------+---------------------------------------------+
   | 双字母代码 | 意义                                        |
   |============|=============================================|
   | "'- '"     | 行为序列 1 所独有                           |
   +------------+---------------------------------------------+
   | "'+ '"     | 行为序列 2 所独有                           |
   +------------+---------------------------------------------+
   | "'  '"     | 行在两序列中相同                            |
   +------------+---------------------------------------------+
   | "'? '"     | 行不存在于任一输入序列                      |
   +------------+---------------------------------------------+

   以 '"?"' 打头的行尝试将视线引至行以外而不存在于任一输入序列的差异。
   如果序列包含制表符则这些行可能会令人感到迷惑。

class difflib.HtmlDiff

   这个类可用于创建 HTML 表格（或包含表格的完整 HTML 文件）以并排地逐
   行显示文本比较，行间与行外的更改将突出显示。 此表格可以基于完全或上
   下文差异模式来生成。

   这个类的构造函数：

   __init__(tabsize=8, wrapcolumn=None, linejunk=None, charjunk=IS_CHARACTER_JUNK)

      初始化 "HtmlDiff" 的实例。

      *tabsize* 是一个可选关键字参数，指定制表位的间隔，默认值为 "8"。

      *wrapcolumn* 是一个可选关键字参数，指定行文本自动打断并换行的列
      位置，默认值为 "None" 表示不自动换行。

      *linejunk* 和 *charjunk* 均是可选关键字参数，会传入 "ndiff()" (
      被 "HtmlDiff" 用来生成并排显示的 HTML 差异)。 请参阅 "ndiff()"
      文档了解参数默认值及其说明。

   下列是公开的方法

   make_file(fromlines, tolines, fromdesc='', todesc='', context=False, numlines=5, *, charset='utf-8')

      比较 *fromlines* 和 *tolines* (字符串列表) 并返回一个字符串，表
      示一个完整 HTML 文件，其中包含各行差异的表格，行间与行外的更改将
      突出显示。

      *fromdesc* 和 *todesc* 均是可选关键字参数，指定来源/目标文件的列
      标题字符串（默认均为空白字符串）。

      *context* 和 *numlines* 均是可选关键字参数。 当只要显示上下文差
      异时就将 *context* 设为 "True"，否则默认值 "False" 为显示完整文
      件。 *numlines* 默认为 "5"。 当 *context* 为 "True" 时
      *numlines* 将控制围绕突出显示差异部分的上下文行数。 当 *context*
      为 "False" 时 *numlines* 将控制在使用 "next" 超链接时突出显示差
      异部分之前所显示的行数（设为零则会导致 "next" 超链接将下一个突出
      显示差异部分放在浏览器顶端，不添加任何前导上下文）。

      在 3.5 版更改: 增加了 *charset* 关键字参数。 HTML 文档的默认字符
      集从 "'ISO-8859-1'" 更改为 "'utf-8'"。

   make_table(fromlines, tolines, fromdesc='', todesc='', context=False, numlines=5)

      比较 *fromlines* 和 *tolines* (字符串列表) 并返回一个字符串，表
      示一个包含各行差异的完整 HTML 表格，行间与行外的更改将突出显示。

      此方法的参数与 "make_file()" 方法的相同。

   "Tools/scripts/diff.py" 是这个类的命令行前端，其中包含一个很好的使
   用示例。

difflib.context_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')

   比较 *a* 和 *b* (字符串列表)；返回上下文差异格式的增量信息 (一个产
   生增量行的 *generator*)。

   所谓上下文差异是一种只显示有更改的行再加几个上下文行的紧凑形式。 更
   改被显示为之前/之后的样式。 上下文行数由 *n* 设定，默认为三行。

   默认情况下，差异控制行（以 "***" or "---" 表示）是通过末尾换行符来
   创建的。 这样做的好处是从 "io.IOBase.readlines()" 创建的输入将得到
   适用于 "io.IOBase.writelines()" 的差异信息，因为输入和输出都带有末
   尾换行符。

   对于没有末尾换行符的输入，应将 *lineterm* 参数设为 """"，这样输出内
   容将统一不带换行符。

   上下文差异格式通常带有一个记录文件名和修改时间的标头。 这些信息的部
   分或全部可以使用字符串 *fromfile*, *tofile*, *fromfiledate* 和
   *tofiledate* 来指定。 修改时间通常以 ISO 8601 格式表示。 如果未指定
   ，这些字符串默认为空。

   >>> s1 = ['bacon\n', 'eggs\n', 'ham\n', 'guido\n']
   >>> s2 = ['Python\n', 'eggy\n', 'hamster\n', 'guido\n']
   >>> sys.stdout.writelines(context_diff(s1, s2, fromfile='before.py', tofile='after.py'))
   *** before.py
   --- after.py
   ***************
   *** 1,4 ****
   ! bacon
   ! eggs
   ! ham
     guido
   --- 1,4 ----
   ! Python
   ! eggy
   ! hamster
     guido

   请参阅 A command-line interface to difflib 获取更详细的示例。

difflib.get_close_matches(word, possibilities, n=3, cutoff=0.6)

   返回由最佳“近似”匹配构成的列表。 *word* 为一个指定目标近似匹配的序
   列（通常为字符串），*possibilities* 为一个由用于匹配 *word* 的序列
   构成的列表（通常为字符串列表）。

   可选参数 *n* (默认为 "3") 指定最多返回多少个近似匹配； *n* 必须大于
   "0".

   可选参数 *cutoff* (默认为 "0.6") 是一个 [0, 1] 范围内的浮点数。 与
   *word* 相似度得分未达到该值的候选匹配将被忽略。

   候选匹配中（不超过 *n* 个）的最佳匹配将以列表形式返回，按相似度得分
   排序，最相似的排在最前面。

   >>> get_close_matches('appel', ['ape', 'apple', 'peach', 'puppy'])
   ['apple', 'ape']
   >>> import keyword
   >>> get_close_matches('wheel', keyword.kwlist)
   ['while']
   >>> get_close_matches('pineapple', keyword.kwlist)
   []
   >>> get_close_matches('accept', keyword.kwlist)
   ['except']

difflib.ndiff(a, b, linejunk=None, charjunk=IS_CHARACTER_JUNK)

   比较 *a* 和 *b* (字符串列表)；返回 "Differ" 形式的增量信息 (一个产
   生增量行的 *generator*)。

   可选关键字形参 *linejunk* 和 *charjunk* 均为过滤函数 (或为 "None")
   ：

   *linejunk*: 此函数接受单个字符串参数，如果其为垃圾字符串则返回真值
   ，否则返回假值。 默认为 "None"。 此外还有一个模块层级的函数
   "IS_LINE_JUNK()"，它会过滤掉没有可见字符的行，除非该行添加了至多一
   个井号符 ("'#'") -- 但是下层的 "SequenceMatcher" 类会动态分析哪些行
   的重复频繁到足以形成噪音，这通常会比使用此函数的效果更好。

   *charjunk*: 此函数接受一个字符（长度为 1 的字符串)，如果其为垃圾字
   符则返回真值，否则返回假值。 默认为模块层级的函数
   "IS_CHARACTER_JUNK()"，它会过滤掉空白字符（空格符或制表符；但包含换
   行符可不是个好主意！）。

   "Tools/scripts/ndiff.py" 是这个函数的命令行前端。

   >>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True),
   ...              'ore\ntree\nemu\n'.splitlines(keepends=True))
   >>> print(''.join(diff), end="")
   - one
   ?  ^
   + ore
   ?  ^
   - two
   - three
   ?  -
   + tree
   + emu

difflib.restore(sequence, which)

   返回两个序列中产生增量的那一个。

   给出一个由 "Differ.compare()" 或 "ndiff()" 产生的 *序列*，提取出来
   自文件 1 或 2 (*which* 形参) 的行，去除行前缀。

   示例:

   >>> diff = ndiff('one\ntwo\nthree\n'.splitlines(keepends=True),
   ...              'ore\ntree\nemu\n'.splitlines(keepends=True))
   >>> diff = list(diff) # materialize the generated delta into a list
   >>> print(''.join(restore(diff, 1)), end="")
   one
   two
   three
   >>> print(''.join(restore(diff, 2)), end="")
   ore
   tree
   emu

difflib.unified_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')

   比较 *a* 和 *b* (字符串列表)；返回统一差异格式的增量信息 (一个产生
   增量行的 *generator*)。

   所以统一差异是一种只显示有更改的行再加几个上下文行的紧凑形式。 更改
   被显示为内联的样式（而不是分开的之前/之后文本块）。 上下文行数由
   *n* 设定，默认为三行。

   默认情况下，差异控制行 (以 "---", "+++" 或 "@@" 表示) 是通过末尾换
   行符来创建的。 这样做的好处是从 "io.IOBase.readlines()" 创建的输入
   将得到适用于 "io.IOBase.writelines()" 的差异信息，因为输入和输出都
   带有末尾换行符。

   对于没有末尾换行符的输入，应将 *lineterm* 参数设为 """"，这样输出内
   容将统一不带换行符。

   上下文差异格式通常带有一个记录文件名和修改时间的标头。 这些信息的部
   分或全部可以使用字符串 *fromfile*, *tofile*, *fromfiledate* 和
   *tofiledate* 来指定。 修改时间通常以 ISO 8601 格式表示。 如果未指定
   ，这些字符串默认为空。

   >>> s1 = ['bacon\n', 'eggs\n', 'ham\n', 'guido\n']
   >>> s2 = ['Python\n', 'eggy\n', 'hamster\n', 'guido\n']
   >>> sys.stdout.writelines(unified_diff(s1, s2, fromfile='before.py', tofile='after.py'))
   --- before.py
   +++ after.py
   @@ -1,4 +1,4 @@
   -bacon
   -eggs
   -ham
   +Python
   +eggy
   +hamster
    guido

   请参阅 A command-line interface to difflib 获取更详细的示例。

difflib.diff_bytes(dfunc, a, b, fromfile=b'', tofile=b'', fromfiledate=b'', tofiledate=b'', n=3, lineterm=b'\n')

   使用 *dfunc* 比较 *a* 和 *b* (字节串对象列表)；产生以 *dfunc* 所返
   回格式表示的差异行列表（也是字节串）。 *dfunc* 必须是可调用对象，通
   常为 "unified_diff()" 或 "context_diff()"。

   允许你比较编码未知或不一致的数据。 除 *n* 之外的所有输入都必须为字
   节串对象而非字符串。 作用方式为无损地将所有输入 (除 *n* 之外) 转换
   为字符串，并调用 "dfunc(a, b, fromfile, tofile, fromfiledate,
   tofiledate, n, lineterm)"。 *dfunc* 的输出会被随即转换回字节串，这
   样你所得到的增量行将具有与 *a* 和 *b* 相同的未知/不一致编码。

   3.5 新版功能.

difflib.IS_LINE_JUNK(line)

   对于可忽略的行返回真值。 *line* 如果为空行或只包含单个 "'#'" 则
   *line* 行是可忽略的，否则就是不可忽略的。 此函数被用作较旧版本
   "ndiff()" 中 *linejunk* 形参的默认值。

difflib.IS_CHARACTER_JUNK(ch)

   对于可忽略的字符返回真值。 *ch* 如果为空格符或制表符则 *ch* 字符是
   可忽略的，否则就是不可忽略的。 此函数被用作 "ndiff()" 中 *charjunk*
   形参的默认值。

参见:

  模式匹配：格式塔方法
     John W. Ratcliff 和 D. E. Metzener 对于一种类似算法的讨论。 此文
     于 1988 年 7 月发表于 Dr. Dobb's Journal。


6.3.1. SequenceMatcher 对象
===========================

"SequenceMatcher" 类具有这样的构造器：

class difflib.SequenceMatcher(isjunk=None, a='', b='', autojunk=True)

   可选参数 *isjunk* 必须为 "None" (默认值) 或为接受一个序列元素并当且
   仅当其为应忽略的“垃圾”元素时返回真值的单参数函数。 传入 "None" 作为
   *isjunk* 的值相当于传入 "lambda x: 0"；也就是说不忽略任何值。 例如
   ，传入:

      lambda x: x in " \t"

   如果你以字符序列的形式对行进行比较，并且不希望区分空格符或硬制表符
   。

   可选参数 *a* 和 *b* 为要比较的序列；两者默认为空字符串。 两个序列的
   元素都必须为 *hashable*。

   可选参数 *autojunk* 可用于启用自动垃圾启发式计算。

   3.2 新版功能: *autojunk* 形参。

   SequenceMatcher 对象接受三个数据属性: *bjunk* 是 *b* 当中 *isjunk*
   为 "True" 的元素集合；*bpopular* 是被启发式计算（如果其未被禁用）视
   为热门候选的非垃圾元素集合；*b2j* 是将 *b* 当中剩余元素映射到一个它
   们出现位置列表的字典。 所有三个数据属性将在 *b* 通过 "set_seqs()"
   或 "set_seq2()" 重置时被重置。

   3.2 新版功能: *bjunk* 和 *bpopular* 属性。

   "SequenceMatcher" 对象具有以下方法：

   set_seqs(a, b)

      设置要比较的两个序列。

   "SequenceMatcher" 计算并缓存有关第二个序列的详细信息，这样如果你想
   要将一个序列与多个序列进行比较，可使用 "set_seq2()" 一次性地设置该
   常用序列并重复地对每个其他序列各调用一次 "set_seq1()"。

   set_seq1(a)

      设置要比较的第一个序列。 要比较的第二个序列不会改变。

   set_seq2(b)

      设置要比较的第二个序列。 要比较的第一个序列不会改变。

   find_longest_match(alo, ahi, blo, bhi)

      找出 "a[alo:ahi]" 和 "b[blo:bhi]" 中的最长匹配块。

      如果 *isjunk* 被省略或为 "None"，"find_longest_match()" 将返回
      "(i, j, k)" 使得 "a[i:i+k]" 等于 "b[j:j+k]"，其中 "alo <= i <=
      i+k <= ahi" 并且 "blo <= j <= j+k <= bhi"。 对于所有满足这些条件
      的 "(i', j', k')"，如果 "i == i'", "j <= j'" 也被满足，则附加条
      件 "k >= k'", "i <= i'"。 换句话说，对于所有最长匹配块，返回在
      *a* 当中最先出现的一个，而对于在 *a* 当中最先出现的所有最长匹配
      块，则返回在 *b* 当中最先出现的一个。

      >>> s = SequenceMatcher(None, " abcd", "abcd abcd")
      >>> s.find_longest_match(0, 5, 0, 9)
      Match(a=0, b=4, size=5)

      如果提供了 *isjunk*，将按上述规则确定第一个最长匹配块，但额外附
      加不允许块内出现垃圾元素的限制。 然后将通过（仅）匹配两边的垃圾
      元素来尽可能地扩展该块。 这样结果块绝对不会匹配垃圾元素，除非同
      样的垃圾元素正好与有意义的匹配相邻。

      这是与之前相同的例子，但是将空格符视为垃圾。 这将防止 "' abcd'"
      直接与第二个序列末尾的 "' abcd'" 相匹配。 而只可以匹配 "'abcd'"
      ，并且是匹配第二个序列最左边的 "'abcd'"：

      >>> s = SequenceMatcher(lambda x: x==" ", " abcd", "abcd abcd")
      >>> s.find_longest_match(0, 5, 0, 9)
      Match(a=1, b=0, size=4)

      如果未找到匹配块，此方法将返回 "(alo, blo, 0)"。

      此方法将返回一个 *named tuple* "Match(a, b, size)"。

   get_matching_blocks()

      Return list of triples describing non-overlapping matching
      subsequences. Each triple is of the form "(i, j, n)", and means
      that "a[i:i+n] == b[j:j+n]".  The triples are monotonically
      increasing in *i* and *j*.

      The last triple is a dummy, and has the value "(len(a), len(b),
      0)".  It is the only triple with "n == 0".  If "(i, j, n)" and
      "(i', j', n')" are adjacent triples in the list, and the second
      is not the last triple in the list, then "i+n < i'" or "j+n <
      j'"; in other words, adjacent triples always describe non-
      adjacent equal blocks.

         >>> s = SequenceMatcher(None, "abxcd", "abcd")
         >>> s.get_matching_blocks()
         [Match(a=0, b=0, size=2), Match(a=3, b=2, size=2), Match(a=5, b=4, size=0)]

   get_opcodes()

      Return list of 5-tuples describing how to turn *a* into *b*.
      Each tuple is of the form "(tag, i1, i2, j1, j2)".  The first
      tuple has "i1 == j1 == 0", and remaining tuples have *i1* equal
      to the *i2* from the preceding tuple, and, likewise, *j1* equal
      to the previous *j2*.

      The *tag* values are strings, with these meanings:

      +-----------------+-----------------------------------------------+
      | 值              | 意义                                          |
      |=================|===============================================|
      | "'replace'"     | "a[i1:i2]" should be replaced by "b[j1:j2]".  |
      +-----------------+-----------------------------------------------+
      | "'delete'"      | "a[i1:i2]" should be deleted.  Note that "j1  |
      |                 | == j2" in this case.                          |
      +-----------------+-----------------------------------------------+
      | "'insert'"      | "b[j1:j2]" should be inserted at "a[i1:i1]".  |
      |                 | Note that "i1 == i2" in this case.            |
      +-----------------+-----------------------------------------------+
      | "'equal'"       | "a[i1:i2] == b[j1:j2]" (the sub-sequences are |
      |                 | equal).                                       |
      +-----------------+-----------------------------------------------+

      例如:

         >>> a = "qabxcd"
         >>> b = "abycdf"
         >>> s = SequenceMatcher(None, a, b)
         >>> for tag, i1, i2, j1, j2 in s.get_opcodes():
         ...     print('{:7}   a[{}:{}] --> b[{}:{}] {!r:>8} --> {!r}'.format(
         ...         tag, i1, i2, j1, j2, a[i1:i2], b[j1:j2]))
         delete    a[0:1] --> b[0:0]      'q' --> ''
         equal     a[1:3] --> b[0:2]     'ab' --> 'ab'
         replace   a[3:4] --> b[2:3]      'x' --> 'y'
         equal     a[4:6] --> b[3:5]     'cd' --> 'cd'
         insert    a[6:6] --> b[5:6]       '' --> 'f'

   get_grouped_opcodes(n=3)

      Return a *generator* of groups with up to *n* lines of context.

      Starting with the groups returned by "get_opcodes()", this
      method splits out smaller change clusters and eliminates
      intervening ranges which have no changes.

      The groups are returned in the same format as "get_opcodes()".

   ratio()

      Return a measure of the sequences' similarity as a float in the
      range [0, 1].

      Where T is the total number of elements in both sequences, and M
      is the number of matches, this is 2.0*M / T. Note that this is
      "1.0" if the sequences are identical, and "0.0" if they have
      nothing in common.

      This is expensive to compute if "get_matching_blocks()" or
      "get_opcodes()" hasn't already been called, in which case you
      may want to try "quick_ratio()" or "real_quick_ratio()" first to
      get an upper bound.

   quick_ratio()

      Return an upper bound on "ratio()" relatively quickly.

   real_quick_ratio()

      Return an upper bound on "ratio()" very quickly.

The three methods that return the ratio of matching to total
characters can give different results due to differing levels of
approximation, although "quick_ratio()" and "real_quick_ratio()" are
always at least as large as "ratio()":

>>> s = SequenceMatcher(None, "abcd", "bcde")
>>> s.ratio()
0.75
>>> s.quick_ratio()
0.75
>>> s.real_quick_ratio()
1.0


6.3.2. 序列匹配的示例
=====================

This example compares two strings, considering blanks to be "junk":

>>> s = SequenceMatcher(lambda x: x == " ",
...                     "private Thread currentThread;",
...                     "private volatile Thread currentThread;")

"ratio()" returns a float in [0, 1], measuring the similarity of the
sequences.  As a rule of thumb, a "ratio()" value over 0.6 means the
sequences are close matches:

>>> print(round(s.ratio(), 3))
0.866

If you're only interested in where the sequences match,
"get_matching_blocks()" is handy:

>>> for block in s.get_matching_blocks():
...     print("a[%d] and b[%d] match for %d elements" % block)
a[0] and b[0] match for 8 elements
a[8] and b[17] match for 21 elements
a[29] and b[38] match for 0 elements

Note that the last tuple returned by "get_matching_blocks()" is always
a dummy, "(len(a), len(b), 0)", and this is the only case in which the
last tuple element (number of elements matched) is "0".

If you want to know how to change the first sequence into the second,
use "get_opcodes()":

>>> for opcode in s.get_opcodes():
...     print("%6s a[%d:%d] b[%d:%d]" % opcode)
 equal a[0:8] b[0:8]
insert a[8:8] b[8:17]
 equal a[8:29] b[17:38]

参见:

  * The "get_close_matches()" function in this module which shows
    how simple code building on "SequenceMatcher" can be used to do
    useful work.

  * Simple version control recipe for a small application built with
    "SequenceMatcher".


6.3.3. Differ Objects
=====================

Note that "Differ"-generated deltas make no claim to be **minimal**
diffs. To the contrary, minimal diffs are often counter-intuitive,
because they synch up anywhere possible, sometimes accidental matches
100 pages apart. Restricting synch points to contiguous matches
preserves some notion of locality, at the occasional cost of producing
a longer diff.

The "Differ" class has this constructor:

class difflib.Differ(linejunk=None, charjunk=None)

   Optional keyword parameters *linejunk* and *charjunk* are for
   filter functions (or "None"):

   *linejunk*: A function that accepts a single string argument, and
   returns true if the string is junk.  The default is "None", meaning
   that no line is considered junk.

   *charjunk*: A function that accepts a single character argument (a
   string of length 1), and returns true if the character is junk. The
   default is "None", meaning that no character is considered junk.

   These junk-filtering functions speed up matching to find
   differences and do not cause any differing lines or characters to
   be ignored.  Read the description of the "find_longest_match()"
   method's *isjunk* parameter for an explanation.

   "Differ" objects are used (deltas generated) via a single method:

   compare(a, b)

      Compare two sequences of lines, and generate the delta (a
      sequence of lines).

      Each sequence must contain individual single-line strings ending
      with newlines.  Such sequences can be obtained from the
      "readlines()" method of file-like objects.  The delta generated
      also consists of newline-terminated strings, ready to be printed
      as-is via the "writelines()" method of a file-like object.


6.3.4. Differ Example
=====================

This example compares two texts. First we set up the texts, sequences
of individual single-line strings ending with newlines (such sequences
can also be obtained from the "readlines()" method of file-like
objects):

>>> text1 = '''  1. Beautiful is better than ugly.
...   2. Explicit is better than implicit.
...   3. Simple is better than complex.
...   4. Complex is better than complicated.
... '''.splitlines(keepends=True)
>>> len(text1)
4
>>> text1[0][-1]
'\n'
>>> text2 = '''  1. Beautiful is better than ugly.
...   3.   Simple is better than complex.
...   4. Complicated is better than complex.
...   5. Flat is better than nested.
... '''.splitlines(keepends=True)

Next we instantiate a Differ object:

>>> d = Differ()

Note that when instantiating a "Differ" object we may pass functions
to filter out line and character "junk."  See the "Differ()"
constructor for details.

Finally, we compare the two:

>>> result = list(d.compare(text1, text2))

"result" is a list of strings, so let's pretty-print it:

>>> from pprint import pprint
>>> pprint(result)
['    1. Beautiful is better than ugly.\n',
 '-   2. Explicit is better than implicit.\n',
 '-   3. Simple is better than complex.\n',
 '+   3.   Simple is better than complex.\n',
 '?     ++\n',
 '-   4. Complex is better than complicated.\n',
 '?            ^                     ---- ^\n',
 '+   4. Complicated is better than complex.\n',
 '?           ++++ ^                      ^\n',
 '+   5. Flat is better than nested.\n']

As a single multi-line string it looks like this:

>>> import sys
>>> sys.stdout.writelines(result)
    1. Beautiful is better than ugly.
-   2. Explicit is better than implicit.
-   3. Simple is better than complex.
+   3.   Simple is better than complex.
?     ++
-   4. Complex is better than complicated.
?            ^                     ---- ^
+   4. Complicated is better than complex.
?           ++++ ^                      ^
+   5. Flat is better than nested.


6.3.5. A command-line interface to difflib
==========================================

This example shows how to use difflib to create a "diff"-like utility.
It is also contained in the Python source distribution, as
"Tools/scripts/diff.py".

   #!/usr/bin/env Python3
   """ Command line interface to difflib.py providing diffs in four formats:

   * ndiff:    lists every line and highlights interline changes.
   * context:  highlights clusters of changes in a before/after format.
   * unified:  highlights clusters of changes in an inline format.
   * html:     generates side by side comparison with change highlights.

   """

   import sys, os, difflib, argparse
   from datetime import datetime, timezone

   def file_mtime(path):
       t = datetime.fromtimestamp(os.stat(path).st_mtime,
                                  timezone.utc)
       return t.astimezone().isoformat()

   def main():

       parser = argparse.ArgumentParser()
       parser.add_argument('-c', action='store_true', default=False,
                           help='Produce a context format diff (default)')
       parser.add_argument('-u', action='store_true', default=False,
                           help='Produce a unified format diff')
       parser.add_argument('-m', action='store_true', default=False,
                           help='Produce HTML side by side diff '
                                '(can use -c and -l in conjunction)')
       parser.add_argument('-n', action='store_true', default=False,
                           help='Produce a ndiff format diff')
       parser.add_argument('-l', '--lines', type=int, default=3,
                           help='Set number of context lines (default 3)')
       parser.add_argument('fromfile')
       parser.add_argument('tofile')
       options = parser.parse_args()

       n = options.lines
       fromfile = options.fromfile
       tofile = options.tofile

       fromdate = file_mtime(fromfile)
       todate = file_mtime(tofile)
       with open(fromfile) as ff:
           fromlines = ff.readlines()
       with open(tofile) as tf:
           tolines = tf.readlines()

       if options.u:
           diff = difflib.unified_diff(fromlines, tolines, fromfile, tofile, fromdate, todate, n=n)
       elif options.n:
           diff = difflib.ndiff(fromlines, tolines)
       elif options.m:
           diff = difflib.HtmlDiff().make_file(fromlines,tolines,fromfile,tofile,context=options.c,numlines=n)
       else:
           diff = difflib.context_diff(fromlines, tolines, fromfile, tofile, fromdate, todate, n=n)

       sys.stdout.writelines(diff)

   if __name__ == '__main__':
       main()
