---
layout: post
title:  "Some Scripts about onlyoffice"
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

在和其项目总架构师某某沟通之后，我就已经明确告知其之 前的技术方案肯定行不通，并给其详细演示了onlyoffice的项目及其相关组件的功能。虽然我目前没有足够的人，但希望其部门能够尽快深入其中，提供一个独立完整的项目组件，并完成和内部平台云的整合。到今天为止，已经小半年过去了。**我都已经把争取功劳的机会，这么直接的摆在他面前了，结果还是如此的效率，我也不好说啥了。**

这里我把onlyoffice相关的项目开源组件做一个整理，顺便将其公司的主要产品和背景的信息也一并加上。我会从具体的项目开始，然后对相关的资料逐步阐述一下。在具体内容的阐述过程中，也会穿插一些软件产品的历史发展的情况。

* 从WPS/MS-Office到office online/google docs/office365
* 从DESKTOP EDITORS到OnlyOffice Online Office Suite
* OnlyOffice VS Google Docs/Office 365/Office Online
* Key Componets Of Onlyoffice Online Suite

-----

### 从WPS/MS-Office到office online/google docs/office36
要说起文档办公软件，绝对可以算是中国软件行业发展的一个最具有代表性的符号。尤其是当年在论坛中看到求伯君那样大神事迹，简直就像是打了一剂充满了鸡血的兴奋剂。以一人之力完成当年WPS，就正如当年比尔盖茨和乔布斯在硅谷打天下的一番场景。
在WPS在中国市场快速发展的时候，微软的office套件利用盗版的漏洞大量的渗透进来，个中的滋味想必求伯君和雷军会有一肚子的话说不出来。

