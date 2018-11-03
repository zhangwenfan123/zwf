---
layout: post
title:  "Brief History of Office"
date:   2018-10-28 23:00:00
categories: onlyoffice office365 
tags:  office software
comments: true
---

* content
{:toc}

[微软今天在Office 365四周年媒体沟通会上宣布，由世纪互联运营的Office 365在华商用的四年中，整体业务增长超过400%，并占据了中国协同协作SaaS市场23%的市场份额，Office 365生产力云平台在中国取得了令人瞩目的业绩和发展成果。](http://baijiahao.baidu.com/s?id=1597993118029904556&wfr=spider&for=pc)
<!--more-->

-----
文章的开头引用了近期office365在SaaS市场领域发展近况的一则新闻。相比对于微软的office，国人肯定是不陌生的。尤其是微软的office桌面三套件，无论是学生时代为了一个激活码各种找后门，还是工作之后，开会时候各种PPT的撰写，在职场的生产力中，office相关的工具几乎已经成为白领工作的代名词。

在本人目前参与的项目中，当初完整的项目号称基于某协同创新平台，项目流弊的不行。结果做的越深，发现坑越多。一直到近期，平台部门整体暂停老项目的维护的开发，将精力全面投入到新的所谓的O**s项目的研发中。首先，我不在上层领导的位置，其做出具体决策的时候究竟在想什么，我不好评价。但是，作为一个平台级别的项目，在基础核心服务的组件上完成度如此之低的情况下，直接来一个大转身，这风险之大稍微想想就知道。

这里我把onlyoffice相关的项目开源组件做一个整理，顺便将其公司的主要产品和背景的信息也一并加上。我会从具体的项目开始，然后对相关的资料逐步阐述一下。在具体内容的阐述过程中，也会穿插一些软件产品的历史发展的情况。

* 从WPS/MS-Office到office online/google docs/office365
* 从DESKTOP EDITORS到OnlyOffice Online Office Suite
* OnlyOffice VS Google Docs/Office 365/Office Online

-----

### 从WPS/MS-Office到office online/google docs/office365
要说起文档办公软件，绝对可以算是中国软件行业发展的一个最具有代表性的符号。尤其是当年在论坛中看到求伯君那样大神事迹，简直就像是打了一剂充满了鸡血的兴奋剂。以一人之力完成当年WPS，就正如当年比尔盖茨和乔布斯在硅谷打天下的一番场景。
在WPS在中国市场快速发展的时候，微软的office套件利用盗版的漏洞大量的渗透进来，个中的滋味想必求伯君和雷军会有一肚子的话说不出来。当然实际的情况要复杂的多，总之随着历史的发展，微软也拿到了中国生产力软件的第一王座。
造纸术被誉为是中国的四大发明之一，是因为随着纸张的成本降低，大大的加速了各种知识和信息的传播。也以一种媒介，产生了很多新的行业，职业，甚至是基于此的各种艺术。单纯从技术上说，办公软件只不过是在冷冰冰的机器上模拟了纸张的一部分功能而已。只不过在传播的过程中，比之前物理的纸张成本更低，效率更高。但是就目前而言，文档的功能更多的是因为关乎公司的经营管理，所以企业投入了更多的精力在纯办公的属性上。也因此，微软的办公软件，甚至是其他的各种工具软件，本质上都是实现了物理纸张的一部分属性，成为了人类发展史上最富有的公司之一。
后来随着互联网的发展，基于浏览器的各种应用程序的发展。微软也推出了在线的office online,这次过程中却被谷歌的docs乘机而入，谷歌docs成为了世界上最受欢迎的协同办公应用。后来随着微软更换领头人，重新将office365放在一个极其重要的战略位置上，并以此推出了其最为重要的云战略。世界上最强大的两家软件公司，在文档这一个战场上，打的不可开交。我相信大部分的公司不是不想做，而是因为技术的壁垒，做不了。并且做了也没有雄厚的实力去支撑相关的云服务和AI的技术为生产力提供持续的动力。

然后说到目前我的项目，卖点也同样在于协同创作，但是其工作平台的排版和创作工具，却远远不能称之为生产力工具。其核心功能最重要的部分依赖于底层的一个C++的中间件。这个中间件本身起源有着悠久的历史，但是却有着极其明显的问题：部分功能规划混乱，耦合度过高，应用层几乎不能处理的所有问题，都丢给了这个中间件。例如本质上来说，其最重要的功能是分布式文件存储，但是其又和文档的复杂转换有着高度的耦合。该中间件投入了大量的人力，几乎就是知网最核心的软件力量了。但是一旦到生产力工具这个级别上，去和对手进行较量。简直就是可以说是落后了一整个世代。


### 从DESKTOP EDITORS到OnlyOffice Online Office Suite

偶然的一个机会，发现一个开源的项目onlyoffice，在详细的了解一段时间，并且使用其桌面端应用工具之后，我十分激动。DESKTOP EDITORS这个应用程序几乎就可以说是当年盘古组件想实现的最终的样子。所有的应用都集中在一个终端，方便的在各种类型的文档之间转换。而且在文档的排版和相关的文档办公操作上，虽然说距离ms-office还有差距，但是从我个人的体验来，轻度，甚至是中度的文档办公操作完全可以胜任了。甚至有一些比较有特色的小功能，让我眼前一亮，简直就是意外之喜。

随着更加深入的使用，我成为了onlyoffice online组件的个人用户，而文档的在线处理工具套件，也是这家公司完全开源的一个工具集。这一点让我十分惊讶，因为文档操作几乎是相关应用最重要的组件部分。微软office365,谷歌docs都没有开源相关的组件，要知道谷歌这样号称开源领头羊的装逼公司，都在外面找不到一个正式的开源组件。其实别说正式项目了，连非正式的都没有。

OnlyOffice Online Office Suite是这家公司推出的一个在线文档的工具集，100%兼容微软的文档格式。基于其文档组件，这家公司衍生出了Projects Manager，Calendar，CRM，Email Server，Community Server，IM等一系列的项目。并且提供基于其各种协作软件平台的云服务，并且可以支持接入多种云平台。

单纯从产品形态上来说，比我们项目的产品更为丰富，生产力工具简直把所有企业内部刚需的部分都做了。而且其中文档的协作，几乎覆盖了某创新平台的整个产品系列。而且就目前而言，由于原先的技术积累有着大量的知识产权，所以肯定不会放弃。但是我觉得，在开源的世界中，如果都这样做的话，那开源几乎就没什么意义了。在平台应用落地的时候，用户的需求成为最强大的推动的时候，却并没有以提升用户体验的最优方案去做。


### OnlyOffice VS Google Docs/Office Online
onlyoffice其官方对于自身产品的能力，几乎是有着俄罗斯名族疯魔般的自信。但是，既然敢在其官网放出自家产品和其他同类巨头的对比，足见其产品本身的扎实。
这里我将其官网和几个主流云平台的对比做一个摘要：
1.*OnlyOffice VS MS Office Online* 

```
a. Feature-rich

ONLYOFFICE offers a complete set of formatting and collaborative tools available online.

b. Self-hosted
ONLYOFFICE can be deployed on your own server providing you with a full control over your data.

c. Open Source
The source code of ONLYOFFICE is listed on GitHub that ensures its transparency and reliability.

```

2.*OnlyOffice VS Google Docs* 

```
a. Compatible
ONLYOFFICE guarantees 100% compatibility with Microsoft Office files and offers the support for DOC, DOCX, ODT, RTF, TXT, PDF, HTML, EPUB, XPS, DjVu, XLS, XLSX, ODS, CSV, PPT, PPTX, ODP.

b. Feature-rich
ONLYOFFICE offers a complete set of formatting tools and collaborative features to work on complex documents online.

c. Self-hosted
With the source code listed on GitHub, ONLYOFFICE can be deployed on your own server providing you with a full control over your data.

```
上面的其总结的优势可以看出来很明显，首先就是对于微软文档原生格式的100%支持，其次就是在协同方面堪比google docs，还有就是最重要的就是项目可以被用户自己部署和配置，完全基于自己的服务和网络。在云服务发展速度如此迅速的情况之下，能够对代码开源到这个程度，可见其云服务的利润有多么巨大。


