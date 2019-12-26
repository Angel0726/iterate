---
title: 推介7个CI  CD 持续集成和持续部署工具
toc: true
date: 2019-11-17
---
# 推介7个CI  CD 持续集成和持续部署工具


**为什么要为CI / CD工作流程使用工具，哪一个适合您？**

![img](https://mmbiz.qpic.cn/mmbiz_png/rAMaszgAyWptRPia6YPOznkCo51GFanBicRkRfHbwumwh1xpfFrprgluJMHBiajY4sVwz4o13F5EgRP8Ik7Lvgvyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

越来越多的工程团队正在采用敏捷开发，推动更短，更快的发布周期。代码库增长和创建新生产构建的频率导致持续集成和持续部署/交付工具的兴起。

如果您还考虑转换到快速发布频率，或者您不确定其他工具提供什么 – 我们已经为您提供保障。在下面的文章中，我们将熟悉一些最流行的CI / CD工具，并逐一了解每一个。船啊！

## 目录

1. Jenkins
2. Travis CI
3. Circle CI
4. TeamCity
5. Codeship
6. GitLab CI
7. Bamboo

## 什么是CI / CD，它有什么用？

在深入研究CI / CD自动化工具之前，我们首先需要了解这个概念。正如我们所提到的，持续集成和持续部署通常与敏捷开发环境齐头并进，在这种环境中，团队希望在完成后立即将不同的代码段部署到生产环境中。

使用CI / CD工具可自动完成构建，测试和部署新代码的过程。每个团队成员都可以立即获得有关其代码生产准备情况的反馈，即使他们只更改了一行或一个字符。这样，每个团队成员都可以将他们的代码推送到生产中，而构建，测试和部署的过程则自动完成，以便他们可以继续处理应用程序的下一部分。

为工作流添加自动化并不会因将代码部署到生产中而结束。您必须先跟踪新错误，然后才能对用户产生重大影响。对于大多数团队而言，在生产中进行调试是一项手动且繁琐的任务，需要他们全程关注日志筛选的数小时和数天。但是，现在可以大规模自动化根本原因分析，了解错误发生的地点，时间和最重要的原因。

如果您是Java，Scala或.NET开发人员，我们会为您提供特别的待遇，请查看。

既然我们知道为什么在我们的工作流程中使用CI模型实现自动化很重要，那么现在是时候看看哪个工具对我们来说是正确的。

## Jenkins

jenkins是CI市场中最知名和最常见的名字之一。它最初是由Sun的一位工程师组成的一个辅助项目，并扩展为最大的开源CI工具之一，可帮助工程团队自动化部署。完全披露：我们OverOps也使用Jenkins以及自己开发的CLI工具。

### 它有什么作用？

就像CI工具一样，Jenkins可以自动构建，测试和部署任务。该工具支持Windows，Mac OSX和各种Unix系统，可以使用本机系统软件包以及Docker进行安装，也可以在安装了Java Runtime Environment（JRE）的任何机器上独立安装。

在实践方面，Jenkins让团队中的任何成员都能够将他们的代码推送到构建中，并立即获得有关它是否已准备好生成的反馈。在大多数情况下，这需要根据您团队的自定义要求对Jenkins进行一些修补和定制。

Jenkins闪耀的地方是其丰富的插件生态系统。它提供了超过1,000个插件的扩展版本，可以集成几乎所有市场上可用的工具和服务。作为一个开源工具，您还可以选择自定义适合本土解决方案，就像我们一样。然而，需要花时间和一些努力来确保它适合你可能是一些团队的缺点。

**价格：**免费

**还有一件事：**我们曾经说过一次，我们会再说一遍：开源+插件=社区。您可以想到的任何配置，工作流程，需求或愿望，您都可以选择在Jenkins及其插件的帮助下创建它。此外，乐队的名字。

**一句话：**如果您正在寻找便宜（免费！）CI解决方案，愿意投入工作来定制您的环境并需要用户社区的支持，Jenkins是您的最佳选择。



![img](https://mmbiz.qpic.cn/mmbiz_jpg/rAMaszgAyWptRPia6YPOznkCo51GFanBicqywMrMMN9icQqbf7EExldPsnwScZrGJc3ZiaQ9ialmJm5uMOM5Iiciba0jg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)*标准Jenkins工作清单*



## 2.Travis CI

Travis CI是CI / CD生态系统中比较常见的名称之一，为开源项目创建，然后多年来扩展到闭源项目。它专注于CI级别，通过自动化测试和警报系统提高构建过程的性能。

### 它有什么作用？

Travis-CI专注于允许用户在部署代码时快速测试代码。它支持大小代码更改，旨在识别构建和测试中的更改。检测到更改后，Travis CI可以提供有关更改是否成功的反馈。

开发人员可以使用Travis CI在运行时观察测试，并行运行多个测试，并将该工具与Slack，HipChat，Email等集成，以获得问题或不成功构建的通知。

Travis CI支持容器构建，并支持Linux Ubuntu和OSX。您可以在不同的编程语言中使用它，例如Java，C＃，Clojure，GO，Haskell，Swift，Perl等等。它有一个有限的第三方集成列表，但由于重点是CI而不是CD，它可能不是您的用例的问题。

**价格：**虽然Travis CI为开源项目提供免费支持，但私人项目的价格从自助版本的69美元/月到高级版本的489美元/月不等。

**还有一件事：**为确保始终备份最近的构建版本，Travis CI会在您运行新构建时将GitHub存储库克隆到新的虚拟环境中。

**结论：**如果您的代码是开源的，并且您更关注构建的持续集成，那么Travis CI值得一试。



![img](https://mmbiz.qpic.cn/mmbiz_gif/rAMaszgAyWptRPia6YPOznkCo51GFanBicJwq6QZQKp8wbUTnicJibqV4KUwG4dhuuH0O1Rp9AOSzV3GQcibFdFbzjA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)*Travis CI仪表板*



## 3.Circle CI

Circle CI是一个基于云的工具，可自动执行集成和部署过程。它还侧重于在部署之前测试代码的每个更改，使用多种方法，如单元测试，集成测试和功能测试。该工具支持容器，OSX，Linux，可以在私有云或您自己的数据中心内运行。

### 它有什么作用？

Circle CI与您当前的版本控制系统（如GitHub，Bitbucket等）集成，并在检测到更改时运行多个步骤。这些更改可能是提交，打开PR或代码的任何其他更改。

每个代码更改都会根据您的初始配置和首选项创建构建并在干净容器或VM中运行测试。每个构建都包含许多步骤，包括依赖性，测试和部署。如果构建通过测试，则可以通过AWS CodeDeploy，Google容器引擎，Heroku，SSH或您选择的任何其他方法进行部署。

有问题的构建和测试的成功或失败状态通过Slack，HipChat，IRC或许多其他集成发送，因此团队可以保持更新。重要的是要注意Circle CI需要对许多语言进行一些调整和更改，因此最好查看所选语言的文档。

**价格：**对于Linux用户，第一个容器是免费的，每个额外的容器每月50美元。对于建造1-5个建筑/天的团队，以及私人数据中心或云计算，OSX价格起价为39美元/月，年度合同的价格为每用户35美元/月。

**还有一件事：**Circle CI可以自动取消GitHub上的冗余构建。如果在同一分支上触发了较新的构建，则该工具会识别它并取消正在运行或排队的旧构建，即使构建未完成也是如此。

**一句话：**如果你正在寻找一个GitHub友好工具，它背后有一个广泛的社区，它也可以在私有云或你自己的数据中心内运行，Circle CI值得一试。



![img](https://mmbiz.qpic.cn/mmbiz_gif/rAMaszgAyWptRPia6YPOznkCo51GFanBicCIY73RCciaGeMU2A8NU1xVM8rWAE6x3NFQZGBYWthdPGkc0xA1kqN0g/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)*Circle CI仪表板*



## 4. TeamCity

TeamCity是由JetBrains制作的CI / CD服务器。它提供“开箱即用”的持续集成，并允许用户根据自己的需要最好地适应工具。它支持多种语言（Java，.NET，Ruby等），并且JetBrains支持工具支持和文档明智。

### 它有什么作用？

作为CI / CD工具，TeamCity旨在改善发布周期。有了它，您可以即时查看测试结果，查看代码覆盖率并查找重复项，以及自定义构建持续时间，成功率，代码质量和其他自定义指标的统计信息。

一旦TeamCity在您的版本控制系统中检测到更改，它就会向队列添加构建。服务器找到空闲兼容的构建代理，并将排队的构建分配给此代理，该代理执行构建步骤。

在此过程运行时，TeamCity服务器会记录不同的日志消息，测试报告以及正在进行的其他更改。这些更改会实时保存和上传，因此用户可以在构建更改时了解构建过程中发生的情况。该工具还提供了在不同平台和环境中同时运行并行构建的选项。

**价格：**专业服务器许可证是免费提供的，它包括100个构建配置，对所有产品功能的完全访问权限，通过论坛和问题跟踪器支持以及3个构建代理。对于具有3个代理的服务器，企业服务器许可证起价为1,999美元，并且根据您感兴趣的代理商数量增加价格。

**还有一件事：**TeamCity附带了一个gated提交选项，可以防止开发人员破坏版本控制系统中的源代码。这是通过在提交之前远程运行构建以进行本地更改来完成的。

**结论：**TeamCity在过去几年中越来越受欢迎，为市场上的其他CI工具提供了一个不错的选择。如果您有兴趣查看构建和测试，或者想要一个免费且功能强大的CI解决方案，毫无疑问TeamCity值得一试。



![img](https://mmbiz.qpic.cn/mmbiz_png/rAMaszgAyWptRPia6YPOznkCo51GFanBicvsxjUSAxRFcCuenZ6geOYgshkj5a1Hx8yfQPlxsibj2sS566kavkLgQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)*TeamCity仪表板*



## 5.Codeship

Codeship对CI / CD有不同的看法，并提供托管持续集成即服务。该工具最初是为Rails开发人员提供持续集成平台而构建的; 在GitHub上托管他们的代码并部署到Heroku。由于其受欢迎程度和需求，该公司多年来不断扩展以支持其他技术。

### 它有什么作用？

Codeship有两种不同的产品，每种都有其优缺点。Codeship Basic允许通过Web UI和交钥匙部署连接存储库来设置CI / CD流程。它支持预配置的CI环境，并允许多个不同的构建在同一构建VM上运行。

Codeship Pro使用Docker定义CI / CD环境，通过它可以运行构建管道。它具有对构建环境的完全控制，允许您定义在其中运行的内容。Pro版本还允许预分支缓存，设置哪些图像以及工作流的哪个部分被缓存，以及并行部署。

整体而言，Codeship支持多种语言，例如Java，Go，Node.js，Python，Ruby等。在部署方面，Basic版本支持AWS，Heroku，Azure和Kubernetes，而Pro也支持AWS ElasticBeanstalk，Google App Engine和DigitalOcean。

**价格：**免费计划包括每月100个版本，用于无限制的项目，用户和团队。它还提供一个并发构建和一个并行测试管道。

根据您感兴趣的并发构建和并行测试管道的数量，基本和专业计划的价格在49美元至79美元/月之间。

**还有一件事：**Codeship有一个公用的实用程序，脚本和Docker镜像集合，可以与该工具一起使用，该公司甚至指出其中一些可以与其他类似的工具一起使用。此集合包括可自定义的外部服务的部署脚本，用于安装默认情况下未包含在构建VM上的特定软件版本的脚本等。

**结论：**在一个域下提供2种不同的工具可能看起来有点奇怪，但它使Codeship可以选择专注于更适合不同类型客户的各种元素。由于Basic和Pro都是免费提供的，因此对于您的CI需求来说这是一个有趣的选择。




*Codeship仪表板*

![img](https://mmbiz.qpic.cn/mmbiz_png/rAMaszgAyWptRPia6YPOznkCo51GFanBicLDy7fmYby7mIJj3XlxZeiaHiaPQibDojkVVYOE6CssjZKial7MLP30XtQA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 6. GitLab CI

GitLab推出后不久，该团队推出了持续集成服务GitLab CI。除了测试和构建项目之外，该工具还可以将构建部署到您的基础架构，通过了解每段代码的位置，您可以选择跟踪不同的部署。

### 它有什么作用？

GitLab CI作为GitLab的一部分免费提供，并且可以相当快速地设置。要开始使用GitLab CI，首先需要将.gitlab-ci.yml文件添加到存储库的根目录，以及配置GitLab项目以使用Runner。之后，每次提交或推送都将触发具有三个阶段的CI管道：构建，测试和部署。

每个构建的可以分为多个作业，并且可以在多台机器上并行运行。该工具可以立即反馈构建的成功或失败，让用户知道出现了什么问题或者过程中是否存在问题。

**价格：**社区版免费提供。对于包含发行板，代码审查中的多个批准，高级语法搜索和一些其他功能的计划，价格从3.25美元/月开始。

**还有一件事：**GitLab（和GitLab CI）是一个开源项目。换句话说，您可以访问并能够修改GitLab Community Edition和Enterprise Edition源代码。

**一句话：**如果您正在使用GitLab，那么尝试将GitLab CI解决方案作为其中的一部分几乎是明智之举。



![img](https://mmbiz.qpic.cn/mmbiz_png/rAMaszgAyWptRPia6YPOznkCo51GFanBicVNnOexALclGJbBJR8c1NwibXbdmib7g8U4iauzY5DGzAP0N5C4ZlbpCAQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)*GitLab CI仪表板*



## 7.Bamboo

Bamboo是Atlassian产品套件的一部分，与其他工具类似，它提供构建，测试和部署代码并支持多种语言。它与其他与CI循环相关的Atlassian产品（如JIRA和Bitbucket）有很强的集成。

### 它有什么作用？

构建，测试和部署都是Bamboo软件包的一部分，测试部分是在Bamboo Agents的帮助下完成的。与Java监控中的代理类似，Bamboo也提供两种类型; 作为其进程的一部分，本地代理作为Bamboo服务器的一部分运行，而远程代理在其他服务器和计算机上运行。每个代理都分配给与其功能相匹配的构建，这允许将不同的代理分配给不同的构建。

Bamboo提供的主要优势是与Atlassian其他产品（如JIRA和Bitbucket）的紧密联系。使用Bamboo，您可以看到自上次部署以来引入代码的代码更改和JIRA问题。这样，开发人员就可以同步他们的工作流程并始终保持正常运行并知道下一个版本以及修复的内容（应该）。

**价格：**竹子定价是根据代理商的数量。无限制本地代理的基本定价为10美元，最多10个工作，没有远程代理。下一层是800美元，用于无限制的工作和本地代理，以及1个远程代理。其他远程代理商的价格将高达44,000美元。

**还有一件事：**Bamboo带有Atlassian强大的支持，以及公司现有产品的更好的工作流程。如果您想以无缝方式将JIRA和Bitbucket添加到您的CI流程并且愿意为此付费，那么Bamboo值得一试。

**结论：**只要你将它与Bitbucket和JIRA一起使用，Bamboo就是强大的，并愿意为你的CI解决方案付费。



![img](https://mmbiz.qpic.cn/mmbiz_png/rAMaszgAyWptRPia6YPOznkCo51GFanBicLDy7fmYby7mIJj3XlxZeiaHiaPQibDojkVVYOE6CssjZKial7MLP30XtQA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)*Bamboo仪表板*



## 最后的想法

对更快和更短的发布周期的需求导致团队必须找到支持新软件交付方法的工具和工作流程。每周甚至每天或每小时推动生产也意味着将新错误引入生产。现在您已经转移到CI / CD工作流程，下一步是了解完整CI / CD工具链中缺少的链接，以及如何将其添加到工作流程中。



根据自己的需求，可以试试。

# 相关

- [推介7个CI / CD(持续集成和持续部署)工具](https://mp.weixin.qq.com/s?__biz=MzAwNzMyMTcxMg==&mid=2453071665&idx=1&sn=4d5fbeae0135829fb196f0273f22a29b&chksm=8cbda62ebbca2f38a085f680d2c8227f03844f7d213dbb2d5464b25620e01dba16571b09e74e&mpshare=1&scene=1&srcid=0822ccx9A3kXwvt8BB01fFEF#rd)
