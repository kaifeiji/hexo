---
title: 有限状态机的实用介绍
date: 2023-02-21 21:11:14
categories:
- Mastering JS
tags:
- 状态机
---

原文：[A Practical Introduction to Finite State Machines](https://masteringjs.io/tutorials/fundamentals/state-machines)

状态机乍一看像是无趣的抽象话题，但它对JavaScript开发者来说非常有用。

<!-- more -->

[有限状态机](https://www.freecodecamp.org/news/state-machines-basics-of-computer-science-d42855debc66/)可以巧妙的实现一种非常简单的设计模式：一个有效的*状态*列表，和一个允许的*状态切换*列表。

例如，[JavaScript的Promise就是状态机](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)。Promise可能是3种状态之一：

1. pending
1. fulfilled
1. rejected

有两种允许的状态切换：pengding变为fulfilled，pending变为rejected。一旦Promise变为fulfilled，它将一直保持fulfilled状态。可以用以下[状态机图](https://www.visual-paradigm.com/guide/uml-unified-modeling-language/what-is-state-machine-diagram/)，表示这个状态机：

![Promise状态机](/post-images/a-practial-introduction-to-finit-state-machines-2023-02-21-21-12-57.png)

状态机图一般使用圆形或方形表示状态，用线或箭头表示状态切换。

## [Mongoose](https://www.npmjs.com/package/mongoose)的基本用例

假如需要创建一个帖子评论的审核App。评论审核可以用类似Promise的状态机表示。一个评论可能是3种状态之一：

1. 等待审核
2. 通过
3. 拒绝

你只需要展示博客中的通过审核的评论，而管理员可以加载所有待审核的评论。用[Mongoose的Schema](https://mongoosejs.com/docs/guide.html)可以表示如下：

```javascript
const schema = mongoose.Schema({
  state: {
    type: String,
    enum: ['Pending', 'Approved', 'Rejected'],
    required: true,
    default: 'Pending'
  },
  authorId: mongoose.ObjectId,
  comment: String
});

const Comment = mongoose.model('Comment', schema);
```

[Mongoose的`find()`函数](http://thecodebarbarian.com/how-find-works-in-mongoose.html)可以轻松加载所有待审核或所有通过的评论：

```javascript
const pending = await Comment.find({ state: 'Pending' });
```

这个状态机图如下：

![Mongoose状态机](/post-images/a-practial-introduction-to-finit-state-machines-2023-02-21-21-13-11.png)

作为开发者，要保证状态转换的实现。在这里要暴露2个express接口：一个用于通过评论，一个用于拒绝评论。

```javascript
app.put('/comment/:id/approve', async function(req, res) {
  const comment = await Comment.findById(req.params.id);
  if (comment.state !== 'Pending') {
    return res.send(400).json({ message: 'Comment is not pending' });
  }
  comment.state = 'Approved';
  await comment.save();
  res.json({ ok: 1 });
});

app.put('/comment/:id/reject', async function(req, res) {
  const comment = await Comment.findById(req.params.id);
  if (comment.state !== 'Pending') {
    return res.send(400).json({ message: 'Comment is not pending' });
  }
  comment.state = 'Rejected';
  await comment.save();
  res.json({ ok: 1 });
});
```
