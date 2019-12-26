---
title: OCR是什么 背后用了哪些技术
toc: true
date: 2019-11-17
---
# OCR是什么 背后用了哪些技术


今天分享的主要是OCR的部分。腾讯云在OCR上做的一些工作，以及腾讯云目前在云上面开放的OCR的一些服务。**OCR简单来说就是让机器能看懂写的文字**。我们手写的文字比较复杂，什么样子的都有。印刷的文字稍微简单一点，但也同样具有复杂性。今天主要讲的就是这种复杂性，这种服务在日常生活或者工程中遇到不同情况所产生如何处理这些复杂性的能力。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8TmD2YcChkuMLu8sqqUxgs6DsM1wUUbVm59FgAlrVheicLnoOQNjp1Bg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这里分享两个做过的例子。身份证相对来讲很格式化、比较简单东西，可以明确知晓在哪里找到怎样的文字信息。后一个是医院的检查报告，医院的检查报告相对而言复杂一点，它的复杂度在于不只是处理一个医院的一种检查报告，而是需要把不同的医院的检查报告全部做统一处理。这就增加了很大的难度。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8xibTQvm6UFpTZgYOPOTkx5mZauOxIbtDp4Sn52ftAp19z1uvA9AZnew/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在做正式的介绍之前，先说一说关于OCR的历史。OCR历史回溯起来还是很久远的，最早在六七十年代就有过实际的应用。大家都写过信，邮编号码在信封的左上角。这就是最早的OCR的应用。这种技术被使用在了一个非常窄的场景里面，只是要求把填在空格里的数字稳定的有效的检索、识别出来。当时的识别概率能达到92%-93%。这解决一个很大的问题，当时邮寄信都是通过识别码来进行投递的。

这个应用场景后来直接导致了2013年MINST的一个诞生。所有的框架都将它作为例子。它就是来源于这种最早的应用。一些复印机，扫描仪厂商，例如，东芝，佳能、富士通等希望将这项技术应用于扫描仪里面的文字转化成电子文字，便于客户存档。在PDF里面也用到这种技术。

时间到了2015年的时候，谷歌云盘里所有的文件免费提供OCR的服务。即便是提供免费服务仍是一种窄场景，只能使用在Google Doc存储的文件。到今年的5月23日，腾讯云公布了OCR免费接入，以及其它很多AI类的图像应用免费接入。这就意味着可以用手机移动终端或者任何的终端设备采集一些文字的图片后上传到云进行解析。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8ZCADVTL29xZQRLOJ7B5q55hyBAmN6F1r5QiczJKGzrko366QagHOFMA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我用两个维度来描述OCR的应用。

一个维度是标明它是一种表格式的OCR还是通用式的OCR。所谓表格式的OCR比较好理解，就是说要识别的这个东西里面是一种表格制式的，它有特定的规格，什么位置写在什么内容。通用OCR的话就没有这种要求，随便拍一张照片里任何的文字都需要提取出来，并且告知那个文字或者那段文字在哪里。

另外一个维度是印刷体维度和手写体维度。这个比较好理解，但是有很多的应用里面也是处于交界的位置上。手写为和印刷体还有一个交界是因为很多印刷体本身并不是一个非常常用的印刷体。而且可以设计成类似于花体字或者写得比较随意一点。比如说招牌，王老吉或者天津狗不理包子。本身的字体并不是常见的字体，可以算是手写体偏印刷体一点。

OCR难度肯定是表格式的会要容易一些。通用式的是要困难一些。同样手写体要困难一些，印刷体要简单一些，那么这个坐标系里面右下角的就是比较难的应用，左上角就是会稍微简单一点。有任何的OCR实际场景应用的时候，我们经常拜访客户。客户提出要解决某个问题的需求的时候，如果这个落到右下角的话就会比较难。如果落在左上角的话会比较容易解决一些。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8icZMf8DyxOl1jU8NOYJOqa6ia0MUuocVIDjFwvYMnFlWwa7vaBmiaAy0g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们现在腾讯云提供的印刷体的服务基本上都是这些，常用的是通用OCR。往腾讯云里面发一张图片，他会把这个图片里面所有可识别的印刷体的文字全返回出来，并告诉你这个印刷体的文字在这个图片里的位置。除此之外还有一些证件类的，比如驾照、车牌、银行卡、名片等等这些，稍后会逐一的介绍这方面的应用。现在用这四个特征来描述我们的服务，第一我们要求服务是准确的；另外要求我们的服务是完备的，就是说能识别英文也能识别中文，也能识别字符。我们现在可以识别一部分的少数民族文字。英文也是没有问题，其他的文字现在也逐渐的往外拓展一个一个的加到我们的范围里面。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8AcnZbTDyo6LPflpAmNjtlant6cTaZ59gmjxEjicKmrkgUzFEVBwI7Wg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



快速这个特性是很多的应用场景中所要求的，我们的OCR在GPU运行是毫秒级的。在CPU的话时间要长一点。还有就是鲁棒性的问题。在手写体的识别方面我们主要的应用比如手写的备忘，像早期诺基亚有一款可以写字并识别出来。现在所有的手机里面都有这种功能。还有一些业务量较大的单据，如运单。这类业务我们是第一家将手写体应用在实际场景中的。数字的识别率高达90%以上。单字的识别率在15毫秒以内，复杂汉字超过80%。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8zxeSibselicczBWHGT337G87gCwicKrjlIIbLskNZVDLwVAiazQWiaU5HlA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



手写OCR强调；数字的准确率主要原因是因为手写体识别大部分都用在银行业和数字相关的行业。无论你写的地址还是写一张支票，那么数字都是最重要的部分。所以我们非常强调这个数字的准确率。腾讯云的OCR服务在权威测评中也得到了非常好的成绩。在2015年的时候取得了排名第一。ICDAR是一个国际文档与识别大会，它是一个比较权威的在OCR方面的一个会议，每两年举办一次。大家如果有兴趣做一些OCR的实验或者做一些OCR的这种技术性的开发，可以去ICDAR上面找一些对比的方法。

OCR技术本身的挑战有这样几点：一个指拍出来的图像。众所周知所有的图像类的AI第一步都是获取图像。要不然的话怎么分析呢？那么图像拿过来的时候就会产生很多的问题，比如说你所用数据的采集，摄像头等成像的仪器不一样，成像的场景不一样。还有要求可能也不一样。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8Eaf5blRpIbI3lwSQv6SxtaS3wTlyg4jIDIKCQwdbBk1nrib9YR69PkQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



OCR是应用很广泛的一项技术，实际场景中会遇到一种文字倾斜、模糊等等的情况。这是一个技术上的挑战。还有一个就是说语言文字本身，最简单是英文OCR。一般来讲中文稍微简单一点。中文繁体字、手写字，国内少数民族文字等使用场景因为数据来源少，场景复杂难度有所增加。

文字大小不一以及文本背景复杂。主要是取决于场景，基本上所有常用的OCR识别步骤都是这样子的：先做一个版面分析，即确定场景。根据版面分析大概明确了正在分析的是什么（驾照、行驶证或发票等）。进而将下一步的步骤简化到比较简单的环境里，这样有助于提高分析的结果，并且能够快速的分析出答案。

下面是文字检测，以及文字识别。在此之后会有后处理，后处理根据一些语义和环境来把识别出来的错误纠正过来。例如：咖灰，咖后面不可能加一个灰，一般都是咖啡。在使用深度学习网络技术做OCR时，并不是一步一步进行，而是一个网络里面的一个模块和一个模块对应的这种功能。但是整个的流程仍然是这样逻辑的流程。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8libFsx3tLgJ0Yic40oicf2C4UJKF1ncQz57U5hToRykv8tAiaNlfF89Geg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



OCR技术本身的发展可以说是分为了三个阶段。最早的时候不用检测，就像上文讲的信封上数字的识别，不需要做检测。直接使用分类器就可以。早期的技术就是对图像做一些特征提取，后面加上分类器，比较成熟的比如有SVM，能直接得到的分类结果。但在当时应用场景很窄。之后场景扩展：先识别文字的位置然后再把这些文字进行一块一块的切。切到小图像之后再回归原来的过程进行识别。

这种方法存在一个很大的问题就是你前面切的话，后面的误差会累积。再后来有了深度学习这项技术，就开始有端到端的模型。现在大部分学术界里面研究发表的论文都是基于CNN和RNN网络结构的。CNN的作用是图像特征提取，RNN做文字序列的识别。尽管网络结构有很多的变形，但它背后的逻辑仍然和原来的没有太大的变化：都是先从图像上面提取一部分特征，再将图像上的特征对应到文字上。CNN是最常用的一种提取图像的特征的方式所以CNN＋RNN这种网络结构处理图像，最后图像产生一系列文字的特征，最后形成文字的过程。

Attention机制最大的优点在于识别当前的字或词的时候，会考虑到它前后哪些字对这个字有影响。那么在原来没有这个之前一般认为，所有的影响都是一样的。因为存在一定连贯性，每个字（词）都与其上下文存在联系。考虑到这种关系就要对整体的模型和识别率进行提升。同时不同语言里面的联系也有不同，这也为语言的研究也提供了一定的信息。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8Og3goiadR7t6eO73dM1mTJtPUfUJTdkDXgactQuovich9U1Dn9Mx13zw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



这张PPT对应了刚才所讲传统的OCR的流程，先将图片获取进行二制化，来提出可能是文字的部分。再去分割这些字，把这些字分割成一块一块，再将这些一块一块小的图片放到分类器里面来识别这些文字是什么字符。进行字符串汇总之后还会进行自然语言处理的修正，最后反馈正确的结果。目前腾讯云基本上已经不采用这种传统的方式，而是以端到端的方式为主，那么除了端到端的方式根据不同的场景应用，已经产生了一套类似工具集的方法。对于不同的应用场景，只需从工具集里找出最为适配这个场景的工具或者模块，再将它们串起来进行调优，最后形成了整体识别的模型。

接下来先给大家介绍一下腾讯云上的服务，再介绍一下我们做过的一些综合类应用。这两者的差别在于云服务本身具有一定的通用性，基本上每个人都可以在腾讯云申请一个账号，通过标准的API发送图片等等。项目更多是定制化解决一个具体问题的。我们就会根据具体的问题和它产生的流程来开发一套系统或者流程来配合它的实际业务，来提高他们的生产效率。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8oNua1jwPWDBkC5iaFKWEvpmBdczZpO0icGLDuIpAGkicQkHH5lnTApGMA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



身份证识可以说是目前最火的识别项目。很早在我们去酒店住店的时候就有一个叫做人证合一的认证过程，在没有人工智能之前就有这种过程。去酒店住店，前台将身份证号输入到电脑发送至公安授权的某数据库的远程服务器上，之后服务器会返回一张身份证照片，服务员会看这个照片跟你本人是不是一个人，验证完毕你就可以住店了。

现在的技术发展到顾客把自己的身份证插到一个读卡器里，它会把这个身份证信息发到身份数据库里面把照片返还回来，并通过摄像头拍摄脸部信息，将拍摄信息与身份证直接对比。目前这项技术不只是用于住店，包括乘坐高铁等，安保人证票合一等场景里已经应用得越来越多。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8xvRbIN6eM2ZeXYq2NBufpcasfm5TCsSAYhQSicPSs7GQgLNuw0Jiafow/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



名片识别是介于格式化和非格式化通用之间的一种。因为名片它所包含的信息是一定的，总会包含姓名包含工作地点，包括电话号码这些。其所采用的字体各方面也比较恒定，所以说是格式化的。偏向通用是因为各部分内容的位置是不一定的。比如一些特别有创意的名片，经常会用一些符号代替本来应使用的一些字。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8pUPrMBiaXvwdUogsQHFVrrPQDGczTDRJaZRC9ddNt5Cmkp1W9RTOBgw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



车牌识别服务应用范围也比较广。一方面是停车，还有在高速进出口以及交通管理车辆的识别也采用了车牌OCR的识别。车牌OCR的识别主要的难点在于场景多样化以及前端采集设备的不可控。如果设备不是高清的设备放大之后会出现模糊的情况。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8dOWLpRiaYfg4laOgxx7gibyLnAZ7ut5Yts5yic9Q25fbJXtmfD9z8YmbA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



驾驶证、行驶证两个证件的识别一般用于租车以及车辆维修等领域的服务。共享汽车、滴滴都会用类似的服务。OCR在这类证件服务领域最大的难点在于证件的反光。这类证件本身它会有一层膜，拍照的时候可能会有反光。预处理会成为OCR识别重要的模块，这种预处理方式一般都是为这种问题单独开发的，它需要产生什么呢？高动态，就是说这种会非常亮的。需要高动态、标准的归一化过程，需要将识别部分归一成比较一致的图像。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8VToH9JrKNXUcN3Lftg5TENr0xcdwDX7FsY3BqhgHjJakFKZZgqNeibg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

银行卡是这个领域比较常见的服务，银行卡的字体相对比较简单，位置也相对固定，但有的时候字体会变得不好识别，尤其在不同的磨损条件下。发票OCR相对格式比较固定，问题在于发票种类多、发票的字体有时会打印的非常的不清楚。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8AMwmIpJCPpZ1JyJERiccIHDs2S2JRhKPBwvwEu1fSHvich1CSaO07C9A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



通用印刷体OCR是比较常见OCR的产品，对OCR的使用相当一部分都是来自于通用的印刷体。广告识别占比较大。这类OCR最大的难度在于很难预料它的背景是什么样子，字体也是各种各样。在归类方面会认为是一种介于印刷体和手写之间的应用方式。对这类识别首先需要有足够大的字体库，如果还不足以解决问题就需要将手写体的技术也放在里面以保证比较高的准确识别率。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8DeHsxUs3KK9ouRfNViamh69EbpEgSpAxHlXg3zpa4ZlBQhSNuBibc9ibw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



针对这一场景产生的方案可以使准确率达到90%以上。随手拍其实也是通用印刷体常见的应用方式。他的问题也是场景变化比较大，会涉及到光线变化的问题。广告类的话光线变化不会有太多的问题，这类光线角度是一个问题，同时还有拍照手抖带来的图像模糊，以及摆放时产生的文字遮挡……这些都会产生影响。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN83JRKxib69M8zgZjyojcibog8CZ9jdd43xMbvMHXSVNZNnibX24h5mqKow/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



血液的检查单也是我们在做项目中的一部分，医院里打出来的血检单，文字间距非常小，字也小，同时识别的时候还会产生透视畸变。对于这种情况有两种处理方式：第采用超解析度做预处理，我把我的图像先进行一个，可以理解成一个采用了人工智能技术的一个非性能差值，使解析度更高、文字看上去更可识别，在进行识别器识别。第二种方式把刚才那部分集成到网络设计里，最大的好处在于针对这种情况会有比较高的识别准确率，并且识别速度会比较快。它的缺点在于遇到其他的类似的问题的时候还会需要较大的调整才会适用新的场景。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8YSzPicpSpS9gYAZiaCNLyyOAzmUdJKibtjsgz4bMnowDEibGcZnSh683jw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



手写体的识别就是比较困难的事情。我们是第一家把手写体识别应用在实际场景中的。应用的场景以快递运单、银行的支票为主。

以上这些服务在腾讯云上都可以找到相应的服务接口，可以免费使用这些服务来自己搭建一个应用。当你实际需要开发的一个软件，或者需要做一个手写体的识别或者做一个通用的OCR识别的时候都可以直接去调用这些服务来完成应用。

下面都是有明确目标客户的实际OCR应用场景。物流运单的挑战：大概在2010年前后快递业发展得非常迅猛。在当时他们的运单就是必须手写之后录入数据库才能进行投递。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN83qh9szfDmOjQ0eyK0pPULRDibzxBl3xnqaiabTRPwadc1Mstekl5MRQQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



早期采取的都是人工录入的方式，开玩笑的说这可能是是继传呼机之后另外一个打字市场。我们与顺丰共同用手写体的OCR来完成他们的运单录入的过程。这种OCR的方式可以持续工作、准确率高达91%而且保密性更高。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8W3u7WXCpymrI6pickOeDQf1UnskMQiaMxiavh6cZvxtjELibAfGFicYneaQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



现在做的OCR系统，可以日处理一千万单，相当于三千多人三班倒的工作量。泰康认知核保项目，是我们现在在做的，我们也在不断的寻找OCR所能达到的业务和应用的边界。泰康核保复项目：以前需要人工确认一个人当前的身体状况是否能够买这一份健康保险。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8Y3QMicKBeWPXiadTY4ZQykcvPj9Xbx1wJ3hicWibs7ghAcPzjnpo2f5wyg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们的主要目的是开发一种代替原有核保方式的系统，降低对医生或者说有医疗经验的核保人员的需求。通过OCR分析，把这些保单进行格式化、结构化输出。之后进行个人患病风险特征的提取。再通过特征建立预测模型，最终得到核保的结论，这个项目对于OCR来讲最大的难度在于单据格式的种类是众多，来源不一。

第二个难度在于扫描件所产生的图像质量差别非常大，第三设计系统需要对医疗知识有一定的了解。我们采用的方式是除了本身的OCR的设计能力，我们也请到了泰康的医疗专家来共同参与设计，并将知识尽量的加入到系统里面。一方面通过医疗字库来提供OCR字的转化能力；另外一方面在做预测回归时把判断通过机器学习的方式固化、标准化。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/VY8SELNGe96Z4GbYKEAgFeiaIicCccjDN8srp1llEdCr1W3NX1YFK7nKPb2ZYJ4QnUuqicYuxrLAwvxqPMNaz4R1Q/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



未来我们仍会不断的去探索AI特别是OCR的实际前沿应用。对纠错库多丰富一些场景信息，以使系统能够适用更多不同的场景。

Q&A

Q：OCR比如说高考识别的过程中，跟快递的扫描单有什么区别吗？高考卷这些OCR的扫描您那边做过吗？谢谢

A：我们做过但不是高考试卷，是教育的。教育的话其实并不是做只是针对高考一个场景的。其实高考的时候你写得字往往是比运单写的字还要清晰一些的，相对来讲还是要容易一些。比较麻烦的是里面有很多公式，这是比较头疼的一点。还有一点不太一样的，运单的话它的，你想输入的这个东西是有一个比较窄的范围的，你输得无非是地址，地址你可以假设穷尽所有的地址选项。但是高考的话相对来讲发散一点，它并没有这么一个全集在里面。所以总体来讲其实高考那个难度是要比运单的难度稍微大一些。但我们也有一些教育方面的应用，那个方面的话其实我们主要的工作是在公式上面。

Q：我问一下我现在有一个问题，我如果是PDF大量的文件上传上去，因为PDF是扫描的图片，它的文字就可深可浅，当我上传一个PDF扫描实现的时候，我很大的数据量进去的时候这块是怎么做处理的？

A：这个有点难住我了。其实关于P处理的问题刚才那位同事回答更为合适一点。因为我是做算法应用的。

Q：比如说图片的深浅不是切割了很多块，切割了很多块之后块与块之间的顺序有一个拼接，这一块是怎么做到的？

A：现在来讲有很多种方式。我们现在基本上不太建议这种分成小块的方式。至少你可以分成行，分成行的话如果你要做一个RNN的话要比分成块效果好一些。我的建议是说第一先通过一个最熟悉的方法把基本流程搭建起来，之后你会发现其中有一些步骤，那么这些步骤在进行逐渐的优化和合并。因为有的步骤如果是两个步骤，你没有必要用两个步骤，用一个网络可以更好的解决。可能用一个网络效果会更好，所以我基本建议开发思路是这么一个开发思路。

Q：我刚才看到您做泰康项目的时候有很多先验的信息，我想问一下先验的信息对应于模型当中是加在哪些部分的？

A：我们刚开始的时候肯定还是要用后处理或者前处理的方法分开来做。第一实现起来比较简单一点，你可以验证你加的这个先验知识是否真正的对你有帮助。当你确定它有帮助的时候，你把它先独立的分块，之后再进一步的优化形成一个整体的功能。实际的情况可能那种都会遇到，有的会分开两部分，当然这个分开的就有一点技术含量了，这确实是会有一些面向应用场景的设计。


# 相关

- [OCR是什么？背后用了哪些技术？](https://mp.weixin.qq.com/s?__biz=MzI2NDU4OTExOQ==&mid=2247485100&idx=1&sn=5703dbd2eee817a880f41eb70a3d93b6&chksm=eaab1cfcdddc95eaf19d0048d7cd1676301af05f4b6f505abd5a065cbad936518584b65d86d0&mpshare=1&scene=1&srcid=0812VCQwdZz8HJlveYwlRbL2#rd)
