---
title: 最易懂的 Alpha-Beta 剪枝算法详解
toc: true
date: 2019-10-29
---
# 最易懂的 Alpha-Beta 剪枝算法详解

Alpha-Beta剪枝用于裁剪搜索树中没有意义的不需要搜索的树枝，以提高运算速度。

假设α为下界，β为上界，对于α ≤ N ≤ β:

若 α ≤ β  则N有解。
若 α > β 则N无解。

下面通过一个例子来说明Alpha-Beta剪枝算法。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq5eCsF10VmJw1hickMNGCnshmG7o3nzNLqG1wWYHHxKBtxF3XukoeDnrw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



上图为整颗搜索树。这里使用极小极大算法配合Alpha-Beta剪枝算法，正方形为自己（A），圆为对手（B）。



初始设置α为负无穷大，β为正无穷大。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq56mZN8pd9up8K9IKAQoc7KOvQKplWdWklRfU5goAUIloiccqiaNkKUfVA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对于B(第四层)而已，尽量使得A获利最小，因此当遇到使得A获利更小的情况，则需要修改β。这里3小于正无穷大，所以β修改为3。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq5REwxpsVCB2QNZ0YicYzKBtm1Ma7fqDAm7gyic3j9joh0gAklCqNA9JJA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



(第四层)这里17大于3，不用修改β。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq5mmO40fTewnyB7kfcpHibzuHAlsSesKpRmMib7Z1ENOII1uIIP0KE24rQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



对于A(第三层)而言，自己获利越大越好，因此遇到利益值大于α的时候，需要α进行修改，这里3大于负无穷大，所以α修改为3。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq5G9fpRJZibbbQAEtIZGqnA7jKqoe42pmJL5xQFUODZroup8SOQl9fYGg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



B(第四层)拥有一个方案使得A获利只有2，α=3,  β=2, α > β, 说明A(第三层)只要选择第二个方案, 则B必然可以使得A的获利少于A(第三层)的第一个方案。



这样就不再需要考虑B(第四层)的其他候选方案了,因为A(第三层)根本不会选取第二个方案,多考虑也是浪费。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq5qGkDkPjV0VzHARyOIdMQBzkdAYBL1j0pXVRQLK8iaDrCd25kBicqHy2g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



B(第二层)要使得A利益最小,则B(第二层)的第二个方案不能使得A的获利大于β, 也就是3. 但是若B(第二层)选择第二个方案, A(第三层)可以选择第一个方案使得A获利为15, α=15,  β=3, α > β, 故不需要再考虑A(第三层)的第二个方案, 因为B(第二层)不会选择第二个方案。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq5SVke7KnsbticCLpKTqsfvx2oBHaKnhMH1Vp4sE7RUicPFJLS0QRhLia2g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



A(第一层)使自己利益最大,也就是A(第一层)的第二个方案不能差于第一个方案, 但是A(第三层)的一个方案会导致利益为2, 小于3, 所以A(第三层)不会选择第一个方案, 因此B(第四层)也不用考虑第二个方案。



![img](https://mmbiz.qpic.cn/mmbiz_png/QtPIxk7nOVfIVteqHpJ1oHPRdyaBFPq54ibKYQwMUVcrrcaia2hz9b58SrGZriaGz3NdU7W8ukV4kOxewP8icTUekw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



当A(第三层)考虑第二个方案时,发现获得利益为3,和A(第一层)使用第一个方案利益一样。



如果根据上面的分析A(第一层)优先选择了第一个方案,那么B不再需要考虑第二种方案,如果A(第一层)还想进一步评估两个方案的优劣的话, B(第二层)则还需要考虑第二个方案。



若B(第二层)的第二个方案使得A获利小于3,则A(第一层)只能选择第一个方案,若B(第二层)的第二个方案使得A获利大于3,则A(第一层)还需要根据其他因素来考虑最终选取哪种方案。


# 相关

- [【基础算法】最易懂的 Alpha-Beta 剪枝算法详解](https://mp.weixin.qq.com/s?__biz=MzI4MDYzNzg4Mw==&mid=2247487164&idx=1&sn=aa03a2a9c5687ea26136566490c7f30f&chksm=ebb43668dcc3bf7e867153a5c6c42a2da056eb8feba2172d74e12e3fff64d1f416a933f0a009&scene=21#wechat_redirect)
