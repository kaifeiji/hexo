---
title: 又一波低代码平台风潮？
date: 2021-12-06 10:53:20
categories:
- 瞎掰
tags:
- 低代码
---

无代码工具生成的代码总是更复杂，因为它包含了无代码开发环境 + 设计工具 + 解释器或运行时 + 集成器 + 实际生成的代码。如果出了问题，所有部分可能都需要排查。

无代码工具最好只用来生成原型产品。当你确切想清楚想要什么，再找程序员将它写出来，这样可能更快，有利于以后的升级和排查。

——[Hacker New 读者](https://news.ycombinator.com/item?id=29191068)

<!-- more -->

## 低代码

低代码平台已经不是一个可以拿出来吹嘘的新概念了。

10年前就有许多厂商、开发工具推出可视化拖拽组件的开发方式。

国外的Mendix、Outsystems、Salesforce Lightning，国内了解不多，印象比较深刻的也就是钉钉。

低代码平台的要素：图形用户界面(GUI)、拖放式组件、模型驱动逻辑，基本已经成了标配。

## 成本

写代码也好，做平台也好，最终都是要实现商业价值的，就必须要考虑成本。

低代码平台对老板们最大的吸引力，也许就是这一条：可以招点水平一般的搞项目，甚至直接让项目经理搭个系统给用户。

听起来非常不错，但是，开发低代码平台本身，需要比较高的水平，还要能理解、拆分业务，能从不同高度和维度思考问题。

一个低代码平台，至少要绑定几位这样的大牛的运维，才能保证稳定、健康运行。

而且，同一个业务/模块的代码，可能会同时存在于设计器、解释器、集成器中，这对于维护是一个非常不利的因素。

## 收益

总体人力成本的降低，算是最大的收益。

用平台绑定客户，也是比较隐形的收益。

## 优势

CRM、ERP、SAP、OA，都是很适合做低代码的系统类型。

从广义上讲，Wordpress、Hexo等博客系统，也算是低代码平台吧。

能把重复的、可配置的交给配置人员、产品经理甚至客户自己做，能少写代码，对程序员也是好事。

低代码本质上，就像各种库包、各种SDK、Iaas、SaaS，封装抽象，降低使用的复杂度，是符合发展规律的。

## 劣势

我司也弄过一个低代码平台，项目启动后，用平台能非常快速的搭出原型。

但随着客户/产品不断提需求，实现难度直线上升，要么平台实现不了，得等大牛升级平台，要么最后变成在配置里写代码。

举个高端点的例子，SAP的ERP软件就号称不用编码实现个性化需求，但是实际上是内置的海量流程和功能，让人用海量的配置来选择。

另外，让程序员去拿平台搞配置，我相信大部分程序员是排斥、抵制的。

而让非专业人士使用，还是需要一定的培训，毕竟低代码平台还实现不了用嘴编程，还是要有一定思维转换和配置套路的。

## 适用场景

软件工程没有银弹，低代码比较适用于业务相对清晰简单的系统。

从用户需求出发，一个需求是一个点，很多个需求就会相互交叉、关联，形成树状或网状的结构。

低代码平台为了实现需求，可以封装编程细节，也能抽象压缩系统复杂度，但对于业务本身的复杂度是无能为力的。

业务复杂度超过一个临界点，用低代码平台搭建、配置一个系统，会比写代码更复杂。

由此推论，在形成标准化的行业，标准化限定了变量，变量少复杂度低，更容易设计出好用的低代码平台。