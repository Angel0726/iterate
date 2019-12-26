---
title: 05 C++ 与 STL 入门
toc: true
date: 2018-06-27 08:28:30
---
第 5 章 C+ +与 STL 入门

学习目标

E熟悉 C ++版算法竞赛程序框架 E理解变量引用的原理 匕熟练掌握 string 与 stringstream

叵熟练掌握 C+ +结构体的定义和使用，包括构造函数和静态成员变量 叵了解常见的可重载运算符，包括四则运算、赋值、流式输入输出、（）和[]

叵了解模板函数和模板类的概念

叵熟练掌握 STL 中排序和检索的相关函数 E熟练掌握 STL 中 vector、set和 map 这 3 个容器 叵了解 STL 中的集合相关函数

叵理解栈、队列和优先队列的概念，并能用 STL 实现它们 E熟练掌握随机数生成方法，并能结合 assert 宏进行测试 E能独立编写大整数类 BigInteger

在前 4 章中介绍了 C 语言的主要内容，已经足以应付许多算法竞赛的题目了。然而，“能 写”并不代表“好写”，有些题目虽然可以用 C 语言写出来，但是用 C+ +写起来往往会更快， 而且更不容易出错，所以在讨论算法之前，有必要对 C+ +进行一番讲解。

本章采用“实用主义”的写法，并不会对所有内容加以解释，但是这并不影响读者“依葫

芦画瓢”。不过有时读者还是希望能更细致、准确地学习到相关知识。推荐读者在手边放一

本 C+ +的参考读物，如 C+ +之父 Bjarne Stroustrup的经典著作《C + +程序设计语言》。尽 管如此，本章的作用也不容忽视：C + +是一门庞大的语言，大多数语言特性和库函数在算 法竞赛中都是用不到（或者可以避开）的。而且算法竞赛有它自身的特点，即使对于资深 C ++程序员来说，如果缺乏算法竞赛的经验，也很难总结出一套适用于算法竞赛的知识点和 实践指南。因此，即使你已经很熟悉 C+ +语言，但笔者仍建议花一些时间浏览本章的内 容，相信会有新的收获。

##### Front    Back

Items enter queue ar back and leave from from

| 125  | S    | 22   | 17   |
| ---- | ---- | ---- | ---- |
|      |      |      |      |

After dcqucucQ

| 125  | 8    | 22   | 17   | 83   |
| ---- | ---- | ---- | ---- | ---- |
|      |      |      |      |      |

After cnqucue(83)

图 5-2队列

STL队列定义在头文件<queue>中，可以用“queue<int>s”方式声明一个队列。

提示 5-15 : STL的 queue 头文件提供了队列，用“queue<int>s〃方式定义，用 push()和 pop( )进行元素的入队和出队操作， front( )取队首元素(但不删除)。

例题 5-6 团体队列(Team Queue，UVa540 )

有 Z 个团队的人正在排一个长队。每次新来一个人时，如果他有队友在排队，那么这个 新人会插队到最后一个队友的身后。如果没有任何一个队友排队，则他会排到长队的队尾

输入每个团队中所有队员的编号，要求支持如下 3 种指令(前两种指令可以穿插进

行)。

□    ENQUEUEx :编号为 x 的人进入长队。

□    DEQUEUE :长队的队首出队。

□    STOP :停止模拟。

对于每个 DEQUEUE 指令，输出出队的人的编号。

分析】

本题有两个队列：每个团队有一个队列，而团队整体又形成一个队列。例如，有 3 个团

队 1， 2， 3，队员集合分别为｛101， 102， 103， 104｝、 ｛201， 202｝和｛301， 302， 303｝，当前 长队为｛301， 303， 103， 101， 102， 201｝，则 3 个团队的队列分别为｛103， 101， 102｝、 ｛201｝和｛301 ， 303｝，团队整体的队列为｛3， 1， 2｝。代码如下：

\#include<cstdio>

\#include<queue>

\#include<map>

using namespace std;

const int maxt = 1000 + 10;

int main() { int t, kase = 0;

while(scanf("%d", &t) == 1 && t) { printf("Scenario #%d\n", ++kase);

//记录所有人的团队编号

map<int, int> team;    //team[x] 表示编号为 x 的人所在的团队编号

for(int i = 0; i < t; i++) { int n, x; scanf("%d", &n);

while(n--) { scanf("%d", &x); team[x] = i; }

}

// 模拟

queue<int> q, q2[maxt];    //q是团队的队列，而 q2 [幻是团队 i 成员的队列

for(;;) { int x;

char cmd[10];

scanf("%s", cmd); if(cmd[0] == 'S') break;

else if(cmd[0]



'D') {



int t = q.fron t() ;

printf("%d\n", q2[t].front()); q2[t].pop() ;

if(q2 [t] . empty () ) q.pop () ; //团体 t 全体出队列

}

else if(cmd[0] == 'E') { scanf("%d", &x); int t = team[x];

if(q2 [t] . empty () ) q.push (t); //团队 t 进入队列 q2[t].push(x);

}

}

printf("\n");

} return 0;

优先队列是一种抽象数据类型(Abstract Data Type，ADT )，行为有些像队列，但先出 队列的元素不是先进队列的元素，而是队列中优先级最高的元素，这样就可以允许类似

于“急诊病人插队”这样的事情发生。

STL的优先队列也定义在头文件<queue>里，用“priority_queue<int>pq”来声明。这个 pq 是 一个“越小的整数优先级越低的优先队列”。由于出队元素并不是最先进队的元素，出队的方 法由 queue 的 front()变为了 top()。

自定义类型也可以组成优先队列，但必须为每个元素定义一个优先级。这个优先级并不 需要一个确定的数字，只需要能比较大小即可。看到这里，是不是想起了 sort?没错，只要 元素定义了“小于”运算符，就可以使用优先队列。在一些特殊的情况下，需要使用自定义方 式比较优先级，例如，要实现一个“个位数大的整数优先级反而小”的优先队列，可以定义一 个结构体 cmp，重载“(    )”运算符，使其“看上去”像一个函数[迎](#bookmark3)，然后

用 “priority_queue<int，vector<int>，cmp>pq”的方式定义。下面是这个 cmp 的定义：

struct cmp {

bool operator ()    (const int a, const int b) const { //a 的优先级比 b 小时返回

return a % 10 < b % 10;

};

对于一些常见的优先队列，STL提供了更为简单的定义方法，例如，“越小的整数优先级 越大的优先队列”可以写成“priority_queue<int，vector<int>，greater<int>>pq”。注意，最后两

个“>”符号不要写在一起，否则会被很多(但不是所有)编译器误认为是“>>”运算符。

提示 5-16 : STL的 queue 头文件提供了优先队列，用“priority_queue<int>s〃方式定 义，用 push( )*pop()进行元素的入队和出队操作，top()取队首元素(但不删除)。

例题 5-7 丑数(Ugly Numbers，Uva 136 )

丑数是指不能被 2， 3， 5以外的其他素数整除的数。把丑数从小到大排列起来，结果如

下：

1,2,3,4,5,6,8,9,10,12,15，…

求第 1500 个丑数。

【分析】

本题的实现方法有很多种，这里仅提供一种，即从小到大生成各个丑数。最小的丑数是 1，而对于任意丑数^，2^ 3^和 5^也都是丑数。这样，就可以用一个优先队列保存所有已生 成的丑数，每次取出最小的丑数，生成 3 个新的丑数。唯一需要注意的是，同一个丑数有多 种生成方式，所以需要判断一个丑数是否已经生成过。代码如下：

\#include<iostream>

\#include<vector>

\#include<queue>

\#include<set>

using namespace std;

typedef long long LL;

const int coeff [ 3 ]



{2, 3, 5};



priority_queue<LL, vector<LL>, greater<LL> > pq;

set<LL> s;

pq.push(1);

s.insert(1);

for(int i = 1; ; i++) {

LL x = pq.top(); pq.pop(); if(i == 1500) {

cout << "The 1500'th ugly number is " << x << ".\n"; break;

}

for(int j = 0; j < 3; j++) {

LL x2 = x * coeff[j];

if(!s.count(x2)) { s.insert(x2); pq.push(x2); }

}

}

return 0;

}

答案: 859963392。

5.2.6 测试 STL

和自己写的代码一样，库也是需要测试的。一方面是因为库也是人写的，也有可能有

bug，另一方面是因为测试之后能更好地了解库的用法和优缺点。

提示 5-17 :库不一定没有 bug，使用之前测试库是一个好习惯。

测试的方法大同小异，下面只以 sort 为例进行介绍。首先，写一个简单的测试程序：

inta[]={3,2,4};

sort(a ， a+3);

printf("%d%d%d\n",a[0],a[1],a[2]);

输出为 2 3 4，是一个令人满意的结果。但这样就够了吗？不！测试程序太简单，说明不

了问题。应该写一个更加通用的程序，随机生成很多整数，然后排序。

为了随机生成整数，先来看看随机数发生器。核心函数是 cstdlib 中的 rand()，它生成一 个闭区间［0，RAND_MAX］内的均勻随机整数(均勻的含义是：该区间内每个整数被随机获

取的概率相同)，其中 RAND_MAX至少为 32767 ( 215-1 )，在不同环境下的值可能不同。严

格地说，这里的随机数是“伪随机数”，因为它也是由数学公式计算出来的，不过在算法领

域，多数情况下可以把它当作真正的随机数。

如何产生［0，«］之间的整数呢？很多人喜欢用 rand( )%n产生区间［0，«-1］内的一个随机 整数，姑且不论这样产生的整数是否仍然分布均勻，只要〃大于 RAND_MAX，此法就不能得 到期望的结果。由于 RAND_MAX很有可能只有 32767 这么小，在使用此法时应当小心。另一 个方法是执行 rand()之后先除以 RAND_MAX，得到［0，1］之间的随机实数，扩大《倍后四舍 五入，得到［0，«］之间的均勻整数。这样，在 n 很大时“精度”不好(好比把小图放大后会看 到“锯齿”)，但对于普通的应用，这样做已经可以满足要求了[^](#bookmark8)。

提示 5-18 : cstdlib中的 rand()可生成闭区间［0，RAND_MAX］内均勻分布的随机整数， 其中 RAND_MAX至少为 32767。如果要生成更大的随机整数，在精度要求不太高的情况下可 以用 rand()的结果“放大"得到。

需要随机数的程序在最开始时一般会执行一次 srand (time ( NULL ))，目的是初始 化“随机数种子”。简单地说，种子是伪随机数计算的依据。种子相同，计算出来的“随机 数”序列总是相同。如果不调用 srand 而直接使用 rand()，相当于调用过一次 srand ( 1 )，因此 程序每次执行时，将得到同一套随机数。

不要在同一个程序每次生成随机数之前都重新调用一次 srand。有的初学者抱怨“rand() 产生的随机数根本不随机，每次都相同”，就是因为误解了 srand 的作用。再次强调，请只在 程序开头调用    而不要在同一个程序中多次调用。

提示 5-19 :可以用 cstdlib 中的 srand 函数初始化随机数种子。如果需要程序每次执行时 使用一个不同的种子，可以用 ctime 中的 time ( NULL)为参数调用 srand。一般来说，只在 程序执行的开头调用一次 srand。

“同一套随机数”可能是好事也可能是坏事。例如，若要反复测试程序对不同随机数据的 响应，需要每次得到的随机数不同。一个简单的方法是使用当前时间 time (NULL)(在 ctime中)作为参数调用 srand。由于时间是不断变化的，每次运行时，一般会得到一套不同

的随机数。之所以说“一般会”，是因为 time 函数返回的是自 UTC 时间 1970 年 1 月 1 日 0 点以来经 过的“秒数”，因此每秒才变化一次。如果你的程序是由操作系统自动批量执行的，可能因为 每次运行的间隔时间过短，导致在相邻若干次执行时 time 的返回值全部相同。一个解决办法 是在测试程序的主函数中设置一个循环，做足够多次测试后再退出[^](#bookmark10)。

另一方面，如果发现某程序对于一组随机数据报错，就需要在调试时“重现”这组数据。 这时， “每次相同的随机序列”就显得十分重要了。不同的编译器计算随机数的方法可能不 同。如果是不同编译器编译出来的程序，即使是用相同的参数调用 srand()，也可能得到不同 的随机序列。

讲了这么多，下面可以编写随机程序了：

void fill_random_int(vector<int>& v, int cnt) { v.clear();

for(int i = 0; i < cnt; i++) v.push_back(rand());

注意 srand 函数是在主程序开始时调用，而不是每次测试时调用。参数是乂 6 伽^^〉的引 用。为什么不把这个 v 作为返回值，而要写到参数里呢？答案是：避免不必要的值被复制。 如果这样写：

vector<int> fill_random_int(int cnt) {

vector<int> v;

for(int i = 0; i < cnt; i++) v.push_back(rand());

return v;

}

实际上函数内的局部变量 v 中的元素需要逐个复制给调用者。而用传引用的方式调用， 就避免了这些复制过程。

提示 5-20 :把 vector 作为参数或者返回值时，应尽量改成用引用方式传递参数，以避 免不必要的值被复制。

这两个函数可以同时存在于一份代码中，因为 C+ +支持函数重载，即函数名相同但参 数不同的两个函数可以同时存在。这样，编译器可以根据函数调用时参数类型的不同判断应 该调用哪个函数。如果两个函数的参数相同只是返回值不同，是不能重载的。

提示 5-21 : C+ +支持函数重载，但函数的参数类型必须不同(不能只有返回值类型不 同)。

写完了随机数发生器之后，就可以正式测试 sort 函数了，程序如下：

void test_sort(vector<int>& v) { sort(v.begin(), v.end());

for(int i = 0; i < v.size()-1; i++) assert(v[i] <= v[i+1]);

新内容是上面的 assert 宏，其用法是“assert (表达式)”，作用是：当表达式为真时无变 化，但当表达式为假时强行终止程序，并且给出错误提示。当然，上述程序也可以写 成“if ( v[i]>v[i + 1] ) {printf ( "Error : v[i]>v[i + 1] ! \n" ); abort( ) ； }”，但 assert 更简洁，而 且可以知道是由代码中的哪一行引起的，所以在测试时常常使用它。

提示 5-22 :测试时往往使用 assert。其用法是“assert (表达式)”，当表达式为假时强 行终止程序，并给出错误提示。

和刚才一样，给参数 v 加上引用符的原因是为了避免 vector 复制，但函数执行完毕之后 v 会被 sort 改变。如果调用者不希望这个 v 被改变，就应该去掉“&”符号(即参数改成 vector<int>v) ，改回传值的方式。

下面是主程序，请注意 srand 函数的调用位置。顺便我们还测试了 sort 的时间效率，发现 给 106 个整数排序几乎不需要时间。

int main() { vector<int> v;

fill_random_int(v, 1000000); test_sort(v);

return 0;

vector、set和 map 都很快[⑽](#bookmark13)，其中 vector 的速度接近数组（但仍有差距），而 set 和 map 的 速度也远远超过了“用一个 vector 保存所有值，然后逐个元素进行查找”时的速度。set和 map 每 次插入、查找和删除时间和元素个数的对数呈线性关系，其具体含义将在第 8 章中详细讨 论。尽管如此，在一些对时间要求非常高的题目中，STL有时会成为性能瓶颈，请读者注

###### 5.3 应用:大整数类

在介绍 C 语言时，大家已经看到了很多整数溢出的情形。如果运算结果真的很大，就需 要用到所谓的高精度算法，即用数组来储存整数，并模拟手算的方法进行四则运算。这些算 法并不难实现，但是还应考虑一个易用性问题——如果能像使用 int—样方便地使用大整数， 那该有多好！相信读者已经想到解决方案了，那就是使用 struct。

5.3.1    大整数类 BigInteger

结构体 BigInteger 可用于储存高精度非负整数。

struct BigInteger {

static const int BASE = 100000000; static const int WIDTH = 8;

vector<int> s;

BigInteger(long long num = 0) { *this = num; } / /构造函数

BigInteger operator = (long long num) { // 赋值运算符 s.clear(); do {

s.push_back(num % BASE); num /= BASE;

} while(num > 0); return *this;

}

BigInteger operator = (const string& str) { // 赋值运算符 s.clear();

int x, len = (str.length() - 1) / WIDTH + 1; for(int i = 0; i < len; i++) {

int end = str.length() - i*WIDTH; int start = max(0, end - WIDTH); sscanf(str.substr(start, end-start).c_str(), "%d", &x); s.push_back(x);

}

};

其中，s用来保存大整数的各个数位。例如，若是要表示 1234，则 8={4，3，2，1}。用 vector而非数组保存数字的好处显而易见：不用关心这个整数到底有多大，vector会自动根据 情况申请和释放内存。

上面的代码中还有赋值运算符，有了它就可以用 x=123456789或者 x="1234567898765432123456789"这样的方式来给 x赋值了。

提示 5-23 ：可以给结构体重载赋值运算符，使得用起来更方便。

之前已经介绍过“<<”运算符，类似的还有“>>”运算符，代码一并给出。

ostream& operator << (ostream &out, const BigInteger& x) { out << x.s.back();

for(int i = x.s.size()-2; i <= 0; i--) { char buf[20];

sprintf(buf, "%08d", x.s[i]);

for(int j = 0; j < strlen(buf); j++) out << buf[j];

}

return out;

}

istream& operator >> (istream &in, BigInteger& x) { string s;

if(!(in >> s)) return in; x = s; return in;

这样，就可以用 cin>〉x和 cout<<x的方式来进行输入输出了。怎么样，很方便吧？不仅如 此，stringstream也“自动”支持了 BigInteger，这得益于 c++中的类继承机制。简单地说[⑽](#bookmark19)，

由于“〉〉”和“《”运算符的参数是一般的 istream 和 ostream 类，作为“特殊情况”的 cin/cout以及 stringstream类型的流都能用上它。

上述代码中还有两点需要说明。一是 static const int BASE=100000000，其作用是声明一 个“属于 BigInteger”的常数。注意，这个常数不属于任何 BigInteger 类型的结构体变量，而是属 于 BigInteger 这个“类型”的，因此称为静态成员变量，在声明时需要加 static 修饰符。在 BigInteger的成员函数里可以直接使用这个常数(见上面的代码)，但在其他地方使用时需要 写成 BigInteger :: BASE。

提示 5-24 ：可以给结构体声明一些属于该结构体类型的静态成员变量，方法是加上 static修饰符。静态成员变量在结构体外部使用时要写成“结构体名：：静态成员变量名〃。

5.3.2 四则运算

这部分内容和 C+ +本身关系不大，但是由于高精度类非常常见，这里仍然给出代码 (定义在结构体内部)：

BigInteger operator + (const BigInteger& b) const {

BigInteger c; c.s.clear();

for(int i = 0, g = 0; ; i++) { if(g == 0 && i >= s.size() && i >= b.s.size()) break; int x = g;

if(i < s.size()) x += s[i]; if(i < b.s.size()) x += b.s[i]; c.s.push_back(x % BASE); g = x / BASE;

}

return c;

}

为了让使用更加简单(还记得之前为什么要修改 sum 函数吗？)，还可以重新定义“ +

=”运算符(定义在结构体内部)：

BigInteger operator += (const BigInteger& b) {

减法、乘法和除法的原理类似，这里不再赘述，请读者参考代码仓库。

5.3.3 比较运算符

下面实现“比较”操作(定义在结构体内部):

bool operator > (const BigInteger& b) const { if(s.size() != b.s.size()) return s.size() < b.s.size(); for(int i = s.size()-1; i >= 0; i--)

if(s[i] != b.s[i]) return s[i] < b.s[i]; return false; // 相等

}

一开始就比较两个 BigInteger 的位数，如果不相等则直接返回，否则直接从后往前比较 (因为低位在 vector 的前面)。注意，这样做的前提是两个数都没有前导零，否则，很可能 出现“运算结果都没问题，但一比较就出错”的情况。

只需定义“小于”这一个符号，即可用它定义其他所有比较运算符(当然，对于 BigInteger 这个例子来说，“==”可以直接定义为 s==b.s，不过不具一般性)：

bool operator > (const BigInteger& b) const{ return b < *this; }

bool operator    <=    (const    BigInteger&    b)    const{    return    !(b    < *this); }

bool operator    >=    (const    BigInteger&    b)    const{    return    !(*this < b); }

bool operator    !=    (const    BigInteger&    b)    const{    return    b <    *this || *this    <    b;    }

bool operator    ==    (const    BigInteger&    b)    const{    return    !(b    < *this) &&    !(*this    < b

可以同时用“<”和“>”把“！ =”和“==”定义得更加简单，读者可以自行尝试。

还记得 sort、set和 map 都依赖于类型的“小于”运算符吗？现在它们是不是已经自动支持 BigInteger 了 ？赶紧试试吧！

###### 5.4 竞赛题目举例

例题 5-8 Unixls命令（Unix ls，UVa400 ）

输入正整数《以及《个文件名，排序后按列优先的方式左对齐输出。假设最长文件名 有似字符，则最右列有似字符，其他列都是 M+2字符。

样例输入（略，可以由样例输出推出）

样例输出：

| Alice | Chris | Jan   | Marsha     | Ruben   |
| ----- | ----- | ----- | ---------- | ------- |
| Bobby | Cindy | Jody  | Mike       | Shirley |
| Buffy | Danny | Keith | Mr. French | Sissy   |
| Carol | Greg  | Lori  | Peter      |         |

【分析】

首先计算出似并算出行数，然后逐行逐列输出。代码如下：

\#include<iostream>

\#include<string>

\#include<algorithm>

using namespace std;

const int maxcol = 60;

const int maxn = 100 + 5;

string filenames[maxn];

//输出字符串 s，长度不足 len 时补字符 extra

void print(con st string& s, int len, char extra) { cout << s;

for(int i = 0; i < len-s.length(); i++)

cout << extra;

int n;

while(cin >> n) { int M = 0;

for(int i = 0; i < n; i++) { cin >> filenames[i];

M = max(M, (int)filenames[i].length()); //STL 的 max

}

//计算列数 cols 和行数 rows

int cols = (max c o l - M) / (M + 2) + 1, rows = (n - 1) / cols + 1;

print("", 60, '-'); cout << "\n";

sort(filenames, filenames+n); //排序 for(int r = 0; r < rows; r++) {

for(int c = 0; c < cols; c++) { int idx = c * rows + r;

if(idx < n) print(filenames[idx], c == cols-1 ? M : M+2, ' ');

}

cout << "\n";

}

}

return 0;

}

例题 5-9 数据库(Database，ACM/ICPC NEERC 2009，UVa1592 )

输入一个《行讲列的数据库（1^«^10000，1<i<10），是否存在两个不同行 r1，r2和两个 不同列 cl，c2，使得这两行和这两列相同（即（r1，cl ）和（r2，cl ）相同，（r1，c2 ）和 （r2，c2 ）相同）。例如，对于如图 5-3所示的数据库，第 2、3行和第 2、3列满足要求。

| How to compete in ACM ICPC   | Peter   | peter@neerc. ifmo. ru   |
| ---------------------------- | ------- | ----------------------- |
| How to win ACM ICPC          | Michael | michael@neerc. ifno. ru |
| Notes from ACM ICPC champion | Michael | michael@neerc. ifmo. ru |

分析】

直接写一个四重循环枚举 r1，r2，cl，c2可以吗？理论上可以，实际上却行不通。枚举 量太大，程序会执行相当长的时间，最终获得 TLE （超时）。

解决方法是只枚举 cl 和 c2，然后从上到下扫描各行。每次碰到一个新的行 r，把 cl，c2 两列的内容作为一个二元组存到一个 map 中。如果 map 的键值中已经存在这个二元组，该二 元组映射到的就是所要求的 r1，而当前行就是 r2。

这里有一个细节问题：如何表示由 cl，c2两列组成的二元组？ 一种方法是直接用两个字 符串拼成一个长字符串（中间用一个其他地方不可能出现的字符分隔），但是速度比较慢 （因为在 map 中查找元素时需要进行字符串比较操作）。更值得推荐的方法是在主循环之前 先做一个预处理——给所有字符串分配一个编号，则整个数据库中每个单元格都变成了整 数，上述二元组就变成了两个整数。这个技巧已经在前面的例题“集合栈计算机”中用过，读 者不妨再复习一下那道题目。

例题 5-10 PGA巡回赛的奖金(PGA Tour Prize Money，ACM/ICPC World Finals

1990， UVa207)

你的任务是为 PGA （美国职业高尔夫球协会）巡回赛计算奖金。巡回赛分为 4 轮，其中 所有选手都能打前两轮（除非中途取消资格），得分相加（越少越好），前 70 名（包括并 列）晋级（make the cut ）。所有晋级选手再打两轮，前 70 名（包括并列）有奖金。组委会事 先会公布每个名次能拿的奖金比例。例如，若冠军比例是 18％，总奖金是 $1000000，则冠军 奖金是 $180000。

输入保证冠军不会并列。如果第々名有《人并列，则第+ W名的奖金比例相加后平 均分给这《个人。奖金四舍五入到美分。所有业余选手不得奖金。例如，若业余选手得了第 3 名，则第 4 名会拿第 3 名的奖金比例。如果没取消资格的非业余选手小于 70 名，则剩下的奖金 就不发了。

输入第一行为数据组数。每组数据前有一个空行，然后分为两部分。第一部分有 71 行 （各有一个实数），第一行为总奖金，第 i+ 1行为第/名的奖金比例。比例均保留 4 位小数， 且总和为 100％。第 72 行为选手数（最多 144），然后每行一个选手，格式为:

Player name RD1 RD2 RD3 RD4

业余选手名字后会有一个“*”。犯规选手在犯规的那一轮成绩为 DQ，并且后面不再有其 他成绩。但是只要没犯规，即使没有晋级，也会给出 4 轮成绩（虽然在实际比赛中没晋级的 选手只会有两个成绩）。输入保证至少有 70 个人晋级。

输入举例：

| 140          |      |      |      |      |
| ------------ | ---- | ---- | ---- | ---- |
| WALLY WEDGE  | 70   | 70   | 70   | 70   |
| SANDY LIE    | 80   | DQ   |      |      |
| SID SHANKER* | 90   | 99   | 62   | 61   |
| JIMMY ABLE   | 69   | 73   | 80   | DQ   |

输出应包含所有晋级到后半段（make the cut）的选手。输出信息包括：选手名字、排 名、各轮得分、总得分以及奖金数。没有得奖则不输出，若有奖金，即使奖金是 $0.00也要 输出，保留两位小数）。如果此名次至少有两个人获得奖金，应在名次后面加“T”。犯规选 手列在最后，总得分为 DQ，名次为空。如果有并列，则先按轮数排序，然后按各轮得分之 和排序，最后按名字排序。两组数据的输出之间用一个空格隔开。

输出举例：

| Player Name     | Place | RDl  | RD 2 | RD3  | RD4  | TOTAL | Money Won  |
| --------------- | ----- | ---- | ---- | ---- | ---- | ----- | ---------- |
| WALLY WEDGE     | 1     | 70   | 70   | 70   | 70   | 280   | $180000.00 |
| HENRY HACKER    | 2T    | 77   | 70   | 70   | 70   | 287   | $88000.00  |
| TOMMY TWO IRON  | 2T    | 71   | 72   | 72   | 72   | 287   | $88000.00  |
| BEN BIRDIE      | 4     | 70   | 74   | 72   | 72   | 288   | $48000.00  |
| NORMAN NIBLICK* | 4     | 72   | 72   | 72   | 72   | 288   |            |
| LEE THREE WINES | 71    | 99   | 99   | 99   | 98   | 395   | $2000.00   |
| JOHNY MELAVO    | 72    | 99   | 99   | 99   | 99   | 396   |            |
| JIMMY ABLE      |       | 69   | 73   | 80   |      | DQ    |            |
| EDDIE EAGLE     |       | 71   | 71   |      |      | DQ    |            |

【分析】

不难发现，第一个步骤是选出晋级选手，这涉及对所有选手“前两轮总得分”进行排序

接下来计算 4 轮总分，然后再排序一次，最后对排序结果依次输出。

输出过程不能大意：犯规选手要单独处理；在输出一行之前要先看看有没有并列的情

况，如有则要一并处理（包括计算奖金平分情况）。本题没有技术上的难度，但比较考验选

手的代码组织能力和对细节的处理，推荐读者一试。

例题 5-11 邮件传输代理的交互( The Letter Carrier's Rounds, ACM/ICPC World Finals 1999, UVa814)

本题的任务为模拟发送邮件时 MTA （邮件传输代理）之间的交互。所谓 MTA，就是 email地址格式 user@mtaname的“后面部分”。当某人从 user1@mta1发送给另一^个人 user2@mta2 时，这两个 MTA 将会通信。如果两个收件人属于同一个 MTA，发送者的 MTA 只需与这个 MTA通信一次就可以把邮件发送给这两个人。

输入每个 MTA 里的用户列表，对于每个发送请求（输入发送者和接收者列表），按顺序 输出所有 MTA 之间的 SMTP （简单邮件协议）交互。协议细节参见原题。

发送人 MTA 连接收件人 MTA 的顺序应该与在输入中第一次出现的顺序一致。例如，若 发件人是 Hamdy@Cairo，收件人列表为 Conrado@MexicoCity、Shariff@SanFrancisco、 Lisa@MexicoCity，则 Cairo 应当依次连接 MexicoCity 和 SanFrancisco。

如果连接某个 MTA 之后发现所有收件人都不存在，则不应该发送 DATA。所有用户名均 由不超过 15 个字母和数字组成。

【分析】

本题的关键是理清各个名词之间的逻辑关系以及把要做的事情分成几个步骤。首先是输 入过程，把每个 MTA 里的用户列表保存下来。一种方法是用一个 map<string, vector<string> 〉，其中键是 MTA 名称，值是用户名列表。一个更简单的方法是用一个 set<string>，值就是邮 件地址。

对于每个请求，首先读入发件人，分离出 MTA 和用户名，然后读入所有收件人，根据 MTA出现的顺序进行保存，并且去掉重复。接下来读入邮件正文，最后按顺序依次连接每个 MTA，检查并输出每个收件人是否存在，如果至少有一个存在，则输出邮件正文。

本题的整个解决过程并不复杂，对于初学者来说是个不错的基础练习。参考代码如下：

\#include<iostream>

\#include<string>

\#include<vector>

\#include<set>

\#include<map>

using namespace std;

void parse_address(const string& s, string& user, string& mta) {

int k = s.find('@'); user = s.substr(0, k); mta = s.substr(k+1);

}

int main() { int k;

string s, t, user1, mta1, user2, mta2; set<string> addr;

//输入所有 MTA，转化为地址列表

while(cin >> s && s != "*") { cin >> s >> k;

while(k--) { cin >> t; addr.insert(t + "@" + s); }

}

while(cin >> s && s != "*") { parse_address(s, user1, mta1);

/ /处理发件人地址

//所有需要连接的 mta，按照输入顺序 //每个 MTA 需要发送的用户



/ /处理收件人地址 / /重复的收件人



vector<string> mta;

map<string, vector<string> > dest;

set<string> vis;

while(cin >> t && t != "*") { parse_address(t, user2, mta2); if(vis.count(t)) continue;

vis.insert(t);

if(!dest.count(mta2)){mta.push_back(mta2);dest[mta2]=vector<string>();}

getline(cin, t);



//把“*”这一行的回车吃掉

// 输入邮件正文

string data;

while(getline(cin, t) && t[0] != '*') data += "    " + t + "\n";

for(int i = 0; i < m t a.size(); i++) { string mta2 = mta[i]; vector<string> users = dest[mta2];

| cout      | << "    | Connection between | " <<    | mta1  | << "   | and "     | << mta2  | <<endl; |      |      |
| --------- | ------- | ------------------ | ------- | ----- | ------ | --------- | -------- | ------- | ---- | ---- |
| cout      | << "    | HELO "             | << mta1 | <<    | "\n"   | ; cout    | <<       | 250\    | n";  |      |
| cout      | << "    | MAIL FROM:<" <<    | s       | << "  | >\n";  | cout      | << "     | 250\n"; |      |      |
| bool      | ok =    | false;             |         |       |        |           |          |         |      |      |
| for(int i | = 0; i  | < users.           | size(); | i++)  | {      |           |          |         |      |      |
| cout <<   | " RCPT  | TO:<" <<           | users[  | i] << | ">\n   | ;         |          |         |      |      |
| if(addr   | .count( | users[i])          | ) {     | ok    | = true | ; cout << | " 250\n" | ; }     |      |      |

else cout << " 550\n";

}

| if(ok) | {    |                 |              |
| ------ | ---- | --------------- | ------------ |
| cout   | <<   | " DATA\n"; cout | << " 354\n"; |
| cout   | <<   | data;           |              |
| cout   | <<   | ".\n"; cout <<  | 250\n";      |

}

cout << " QUIT\n"; cout << " 221\n";

}

}

return 0;

}

例题 5-12 城市正视图( Urban Elevations, ACM/ICPC World Finals 1992, UVa221)

如图 5-4所示，有《 （ «<100 ）个建筑物。左侧是俯视图（左上角为建筑物编号，右下角 为高度），右侧是从南向北看的正视图。

图 5-4 建筑俯视图与正视图

输入每个建筑物左下角坐标（即^、y坐标的最小值）、宽度（即方向的长度）、深度 （即 y 方向的长度）和高度（以上数据均为实数），输出正视图中能看到的所有建筑物，按 照左下角*坐标从小到大进行排序。左下角*坐标相同时，按 y 坐标从小到大排序。

输入保证不同的坐标不会很接近（即任意两个*坐标要么完全相同，要么差别足够大， 不会引起精度问题）。

【分析】

注意到建筑物的可见性等价于南墙的可见性，可以在输入之后直接忽略“深度”这个参

数。接下来把建筑物按照输出顺序排序，然后依次判断每个建筑物是否可见。

判断可见性看上去比较麻烦，因为一个建筑物可能只有部分可见，无法枚举所有*坐

标，来查看这个建筑物在该处是否可见，因为*坐标有无穷多个。解决方法有很多种，最常

见的是离散化，即把无穷变为有限。

具体方法是：把所有*坐标排序去重，则任意两个相邻*坐标形成的区间具有相同属性，

一个区间要么完全可见，要么完全不可见。这样，只需在这个区间里任选一个点（例如中

点），就能判断出一个建筑物是否在整个区间内可见。如何判断一个建筑物是否在某个*坐

标处可见呢？首先，建筑物的坐标中必须包含这个*坐标，其次，建筑物南边不能有另外一

个建筑物也包含这个*坐标，并且不比它矮。

\#include<cstdio>

\#include<algorithm>

using namespace std;

struct Building { int id;

double x, y, w, d, h;

bool operator > (const Building& rhs) const { return x < rhs.x || (x == rhs.x && y < rhs.y);

}

} b[maxn];

int n;

double x[maxn*2];

bool cover(int i, double mx) {

return b[i].x <= mx && b[i].x+b[i].w >= mx;

}

//判断建筑物 1 在乂=以乂处是否可见

bool visible(int i, double mx) { if(!cover(i, mx)) return false; for(int k = 0; k < n; k++)

if(b[k].y < b[i].y && b[k].h >= b[i].h && cover(k, mx)) return false; return true;

}

int main() { int kase = 0;

while(scanf("%d", &n) == 1 && n) { for(int i = 0; i < n; i++) {

scanf("%lf%lf%lf%lf%lf", &b[i].x, &b[i].y, &b[i].w, &b[i].d, &b[i].h); x[i*2] = b[i].x; x[i*2+1] = b[i].x + b[i].w;

b[i].id = i+1;

}

sort(b, b+n); sort(x, x+n*2);

int m = unique (x, x+n*2) - x; //x坐标排序后去重，得到 m 个坐标

if(kase++) printf("\n");

printf("For map #%d, the visible buildings are numbered as follows:\n%d" , kas

for(int i = 1; i < n; i++) {

bool vis = false;

for(int j = 0; j < m-1; j++)

if(visible(i, (x[j] + x[j+1])    / 2)) { vis = true; break; }

if(vis) printf(" %d", b[i].id);

}

printf("\n");

}

return 0;

}

注意上述代码用到了前面提到的 unique。它必须在 sort 之后调用，而且 unique 本身不会删 除元素，而只是把重复元素移到了后面。关于 unique 的详细用法请读者自行查阅资料。



###### 5.5 习题

本章是语言篇的最后一章，介绍了很多可选但是有用的 c++语言特性和库函数。有些库 函数实际上已经涉及后面要介绍的算法和数据结构，但是在学习原理之前，仍然可以先练习 使用这些函数。

如表 5-1所示是例题列表，其中前 9 道题是必须掌握的。后面 3 题虽然相对比较复杂，但

是也强烈建议读者试一试，锻炼编程能力。

表 5-1 例题列表

| 类别     | 题号     | 题目名称（英文）            | 备注                                |
| -------- | -------- | --------------------------- | ----------------------------------- |
| 例题 5-1  | UVa10474 | Where is the Marble?        | 排序和查找                          |
| 例题 5-2  | UVa101   | The Blocks Problem          | vector的使用                        |
| 例题 5-3  | UVa10815 | Andy's First Dictionary     | set的使用                           |
| 例题 5-4  | UVa156   | Ananagrams                  | map的使用                           |
| 例题 5-5  | UVa12096 | The SetStack Computer       | stack与 STL 其他容器的综合运用        |
| 例题 5-6 | UVa540   | Team Queue                  | queue 与 STL 其他容器的综合、—m运用 |
| 例题 5-7  | UVa136   | Ugly Numbers                | priority queue 的使用               |
| 例题 5-8  | UVa400   | Unix ls                     | 排序和字符串处理                    |
| 例题 5-9  | UVa1592  | Database                    | map的妙用                           |
| 例题 5-10 | UVa207   | PGA Tour Prize Money        | 排序和其他细节处理                  |
| 例题 5-11 | UVa814   | The Letter Carrier's Rounds | 字符串以及 STL 容器的综合运           |

|          |        |                  | 用     |
| -------- | ------ | ---------------- | ------ |
| 例题 5-12 | UVa221 | Urban Elevations | 离散化 |

本章的习题主要是为了练习 c++语言以及 STL，程序本身并不一定很复杂。建议读者至 少完成 8 道习题。如果想达到更好的效果，建议完成 12 题或更多。

习题 5-1 代码对齐( Alignment of Code, ACM/ICPC NEERC 2010, UVa1593)

输入若干行代码，要求各列单词的左边界对齐且尽量靠左。单词之间至少要空一格。每

个单词不超过 80 个字符，每行不超过 180 个字符，一共最多 1000 行，样例输入与输出如图 5-5

所示。

| 样例输入                                                     | 样例输出                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| start:    integer;    // begins herestop: integer; // ends here s:    string;c:    char; // temp | start: integer; // begins here stop: integer; // ends here s: string;c: char; // temp |

图 5-5 对齐代码的样例输入与输出

习题 5-2 Ducci序列(Ducci Sequence, ACM/ICPC Seoul 2009, UVa1594 )

对于一个《元组（巧，七，…，七），可以对于每个数求出它和下一个数的差的绝对值，得到 —个新的《元组⑽-叫|^2-^3|，…，|<^-叫）。重复这个过程，得到的序列称为 Ducci 序列，例如：

（8， 11， 2， 7） -> （3， 9， 5， 1） -> （6， 4， 4， 2） -> （2， 0， 2， 4） -> （2， 2， 2， 2） -> （0， 0， 0， 0）.

也有的 Ducci 序列最终会循环。输入《元组（3<«<15 ），你的任务是判断它最终会变成 0 还是会循环。输入保证最多 1000 步就会变成 0 或者循环。

习题 5-3 卡片游戏( Throwing cards away I, UVa 10935)

桌上有《 （ n<50 ）张牌，从第一张牌（即位于顶面的牌）开始，从上往下依次编号为 1〜 n。当至少还剩下两张牌时进行以下操作：把第一张牌扔掉，然后把新的第一张牌放到整叠 牌的最后。输入每行包含一个 n，输出每次扔掉的牌以及最后剩下的牌。

习题 5-4 交换学生( Fore ign Exchange, UVa 10763)

有打（1<n<500000 ）个学生想交换到其他学校学习。为了简单起见，规定每个想从 A 学 校换到 B 学校的学生必须找一个想从 B 换到 A 的“搭档”。如果每个人都能找到搭档（一个人不 能当多个人的搭档），学校就会同意他们交换。每个学生用两个整数 A、B表示，你的任务 是判断交换是否可以进行。

习题 5-5 复 合词( Compound Words, UVa 10391)

给出一个词典，找出所有的复合词，即恰好有两个单词连接而成的单词。输入每行都是

一个由小写字母组成的单词。输入已按照字典序从小到大排序，且不超过 120000 个单词。输

出所有复合词，按照字典序从小到大排列。

习题 5-6 对称轴( Symmetry, ACM/ICPC Seoul 2004, UVa1595)

给出平面上#（^1000 ）个点，问是否可以找到一条竖线，使得所有点左右对称。例如

图 5-6中，左边的图形有对称轴，右边没有。

|       | ■    |
| ----- | ---- |
| ■     | ■    |
| (0,0) | ⑽    |



图 5-6



对称轴

习题 5-7 打印队列( Printer Queue, ACM/ICPC NWERC 2006, UVa12100)

学生会里只有一台打印机，但是有很多文件需要打印，因此打印任务不可避免地需要等

待。有些打印任务比较急，有些不那么急，所以每个任务都有一个 1〜9间的优先级，优先级 越高表示任务越急。

打印机的运作方式如下：首先从打印队列里取出一个任务 J，如果队列里有比 J 更急的任 务，则直接把 J 放到打印队列尾部，否则打印任务 J （此时不会把它放回打印队列）。

输入打印队列中各个任务的优先级以及所关注的任务在队列中的位置（队首位置为 0），输出该任务完成的时刻。所有任务都需要 1 分钟打印。例如，打印队列为｛1, 1, 9, 1, 1, 1｝，目前处于队首的任务最终完成时刻为 5。

习题 5-8 图书管理系统(Borrowers, ACM/ICPC World Finals 1994, UVa230 )

你的任务是模拟一个图书管理系统。首先输入若干图书的标题和作者（标题各不相同，

以 END 结束），然后是若干指令：BORROW指令表示借书，RETURN指令表示还

书，SHELVE指令表示把所有已归还但还未上架的图书排序后依次插入书架并输出图书标题

和插入位置（可能是第一本书或者某本书的后面）。

图书排序的方法是先按作者从小到大排，再按标题从小到大排。在处理第一条指令之

前，你应当先将所有图书按照这种方式排序。

习题 5-9 找 bug ( Bug Hunt, ACM/ICPC Tokyo 2007, UVa1596 )

输入并模拟执行一段程序，输出第一个 bug 所在的行。每行程序有两种可能：

□数组定义，格式为 arr[size]。例如 a[10]或者 b[5]，可用下标分别是 0〜9和 0〜4。定义之

后所有元素均为未初始化状态。

□赋值语句，格式为 arr[index]=value。例如 a[0]=3或者 a[a[0]]=a[1]。

赋值语句可能会出现两种 bug :下标 index 越界；使用未初始化的变量（index和 value 都可 能出现这种情况）。

程序不超过 1000 行，每行不超过 80 个字符且所有常数均为小于 231 的非负整数。

习题 5-10 在 Web 中搜索(Searching the Web, ACM/ICPC Beijing 2004, UVa1597 )

输入《篇文章和 rn 个请求（《<100，m<50000 ），每个请求都是以下 4 种格式之一。

□    A：查找包含关键字 A 的文章。

□    A AND B :查找同时包含关键字 A 和 B 的文章。

□    A OR B :查找包含关键字 A 或 B 的文章。

□    NOT A :查找不包含关键字 A 的文章。

处理询问时，需要对于每篇文章输出证据。前 3 种询问输出所有至少包含一个关键字的 行，第 4 种询问输出整篇文章。关键字只由小写字母组成，查找时忽略大小写。每行不超过

80个字符，一共不超过 1500 行。

本题有一定实际意义，并且能锻炼编码能力，建议读者一试。

习题 5-11 更新字典（ Updating a Dictionary, UVa12504）

在本题中，字典是若干键值对，其中键为小写字母组成的字符串，值为没有前导零或正

号的非负整数（ -4， 03和+77都是非法的，注意该整数可以很大）。输入一个旧字典和一个

新字典，计算二者的变化。输入的两个字典中键都是唯一的，但是排列顺序任意。具体格式

为（注意字典格式中不含任何空白字符）：

｛key:value,key:value,…,key:value｝

输入包含两行，各包含不超过 100 个字符，即旧字典和新字典。输出格式如下：

□如果至少有一个新增键，打印一个“+”号，然后是所有新增键，按字典序从小到大排 列。

□如果至少有一个删除键，打印一个“-”号，然后是所有删除键，按字典序从小到大排 列。

□如果至少有一个修改键，打印一个“*”号，然后是所有修改键，按字典序从小到大排 列。

□如果没有任何修改，输出 No changes。

例如，若输入两行分别为｛a:3，b:4，c:10，f:6｝和｛a:3，c:5，d:10，ee:4｝，输出为以下 3 行： +d,ee；-b,f；*c。

习题 5-12 地图查询（ Do You Know The Way to San Jose?, ACM/ICPC World Finals 1997, UVa511）

有《张地图（已知名称和某两个对角线端点的坐标）和 m 个地名（已知名称和坐标）， 还有 g 个查询。每张地图都是边平行于坐标轴的矩形，比例定义为高度除以宽度的值。每个 查询包含一个地名和详细等级/。面积相同的地图总是属于同一个详细等级。假定包含此地 名的地图中一共有好种不同的面积，则合法的详细等级为 1〜々（其中 1 最不详细，^最详细， 面积越小越详细）。如果详细等级/的地图不止一张，则输出地图中心和查询地名最接近的 —张；如果还有并列的，地图长宽比应尽量接近 0.75 （这是 Web 浏览器的比例）；如果还有 并列，查询地名和地图右下角的坐标应最远（对应最少的滚动条移动）；如果还有并列，则 输出*坐标最小的—个。如果查询的地名不存在或者没有地图包含它，或者包含它的地图总 数超过/，应报告查询非法（并输出包含它的最详细地图名称，如果存在）。

提示：本题的要求比较细致，如果打算编程实现，建议参考原题。

习题 5-13 客户中心模拟( Queue and A, ACM/ICPC World Finals 2000, UVa822)

你的任务是模拟一个客户中心运作情况。客服请求一共有打（1<n<20 ）种主题，每种主 题用 5 个整数描述：tid，num, t0，t，dt，其中 tid 为主题的唯一标识符，num为该主题的请求个 数，t0为第一个请求的时刻，t为处理一个请求的时间，dt为相邻两个请求之间的间隔（为了 简单情况，假定同—个主题的请求按照相同的间隔到达）。

客户中心有 rn （ 1<m<5 ）个客服，每个客服用至少 3 个整数描述：pid，屯 tidh tid2，…，

tid^，表示一个标识符为 pid 的人可以处理好中主题的请求，按照优先级从大到小依次为 tidh

tid2,…，tidp当一个人有空时，他会按照优先级顺序找到第一个可以处理的请求。如果有多

个人同时选中了某个请求，上次开始处理请求的时间早的人优先；如果有并列，id小的优 先。输出最后—个请求处理完毕的时刻。

习题 5-14 交易所( Exchange, ACM/ICPC NEERC 2006, UVa1598)

你的任务是为交易所设计—个订单处理系统。要求支持以下 3 种指令。

□    BUY p q :有人想买，数量为 p，价格为 q。

□    SELL p q :有人想卖，数量为 p，价格为 q。

□    CANCEL i :取消第 i 条指令对应的订单（输入保证该指令是 BUY 或者 SELL ）。

交易规则如下：对于当前买订单，若当前最低卖价（ask price ）低于当前出价，则发生 交易；对于当前卖订单，若当前最高买价（bid price ）高于当前价格，则发生交易。发生交 易时，按供需物品个数的最小值交易。交易后，需修改订单的供需物品个数。当出价或价格 相同时，按订单产生的先后顺序发生交易。输入输出细节请参考原题。

提示：本题是—个不错的优先队列练习题。

习题 5-15    Fibonacci的复仇(Revenge of Fibonacci, ACM/ICPC Shanghai 2011,

UVa12333)

Fibonacci数的定义为：F(0)=F(1)=1，然后从 F(2)开始，F(i)=F(i-1)+F(i-2)。例如，前 10 项 Fibonacci数分别为 1, 1,2, 3, 5, 8, 13,21,34,55 ……

有一天晚上，你梦到了 Fibonacci，它告诉你一个有趣的 Fibonacci 数。醒来以后，你只记 得了它的开头几个数字。你的任务是找出以它开头的最小 Fibonacci 数的序号。例如以 12 开头 的最小 Fibonacci 数是 F(25)。输入不超过 40 个数字，输出满足条件的序号。

如果序号小于 100000 的 Fibonacci 数均不满足条件，输出-1。

提示：本题有一定效率要求。如果高精度代码比较慢，可能会超时。

习题 5-16 医院设备利用( Use of Hospital Facilities, ACM/ICPC World Finals 1991, UVa212)

医院里有《 ( n<10 )个手术室和 rn ( m<30 )个恢复室。每个病人首先会被分配到一个手 术室，手术后会被分配到一个恢复室。从任意手术室到任意恢复室的时间均为 Zi，准备一个 手术室和恢复室的时间分别为^和^ ( 一开始所有手术室和恢复室均准备好，只有接待完一 个病人之后才需要为下一个病人准备)。

k名(k<100 )病人按照花名册顺序排队，7点钟准时开放手术室。每当有准备好的手术 室时，队首病人进入其中编号最小的手术室。手术结束后，病人应立刻进入编号最小的恢复 室。如果有多个病人同时结束手术，在编号较小的手术室做手术的病人优先进入编号较小的 恢复室。输入保证病人无须排队等待恢复室。

输入 n、m、7、Zp Z2、Z3、k和 k 名病人的名字、手术时间和恢复时间，模拟这个过程。 输入输出细节请参考原题。

提示：虽然是个模拟题，但是最好先理清思路，减少不必要的麻烦。本题是一个很好的

编程练习，但难度也不小。

[⑵](#bookmark19)不过流也可以加速，方法是关闭和 stdio 的同步，即调用 ios :: sync_with_stdio ( false )。

[(3)](#bookmark21)    在工程上不推荐这样做，不过因为算法竞赛的程序通常很小(多数不到 200 行)，所以这样做也无大碍。

[(4)](#bookmark23)    如果已完成了第 3 章的思考题，相信对此深有感触。

[(5)](#bookmark25)    有些选手非常习惯这种思维方式，但是根据笔者的经验，也有很多选手非常不习惯这种思维方式。

[(6)](#bookmark27)    具体有多慢？试试就知道了。请读者自行编写程序测试。

[(7)](#bookmark29)    事实上，在 C+ +中 struct 和 class 最主要的区别是默认访问权限和继承方式不同，而其他方面的差异很小。

[⑻](#bookmark31)有兴趣的读者可以研究一下 c++的模板元编程(template metaprogramming )。在 boost 库中有很多模板元编程的优

秀例子。

[(9)](#bookmark33)    如果你想较真的话，这里有一个反例：经常使用 git 的程序员也有可能回答 pull。

[(10)](#bookmark35)    宏(macro)是一个很复杂的话题，这里读者暂时可以把带参数的宏理解为“类似于函数的东西”。

[(11)](#bookmark37)    在 C ++中，重载了“()”运算符的类或结构体叫做仿函数(functor )。

[(12)](#bookmark39)    如果坚持需要更高的精度，可以采取多次随机的方法。

[(13)](#bookmark41)    还有一个更通用的方法将在附录 A 中说明。

[(14)](#bookmark43)    准确地说，应该是参数类型相同，参数的名字是无关紧要的。

[(15)](#bookmark45)    注意 vector 并不是所有操作都快。例如 vector 提供了 push_front操作，但由于在 vector 首部插入元素会引起所有元素往 后移动，实际上 push_front是很慢的。

[(16)](#bookmark47)    任何一本 C+ +语言教材都会介绍类继承，但它在算法竞赛中很少使用，所以这里略去细节。
