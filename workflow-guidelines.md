# 翻译流程与规范

欢迎使用 StreamNative Platform 文档。在开始翻译前，请详细阅读翻译[流程](#翻译流程)与[规范](#翻译规范)。

## 翻译流程
StreamNative Platform 文档翻译流程如下。
### 创建翻译分支

参考[创建分支](CONTRIBUTING.md#contribution-workflow)。

### 选择文件
选择需要翻译的 md 文件，直接在此文件内将原文翻译成中文。

> 由于 StreamNative Platform 还在积极开发中，近期会新增很多功能和部署方式。建议查看[文档优先级及说明](README.md#中文文件翻译说明)，选择`优先级高`并且`可翻译`（较稳定）的文档进行翻译。

### 翻译文件
翻译时，需要特别注意：
1. 文件 id 不需要翻译；
2. 译文中的标签需和源文件保持一致。

> **重要  
> 一定不要修改、遗漏标签，也不要在标签后面添加空格。tag 遗漏或错位会造成中文网站内容显示错乱。**

翻译过程中，可以参考 [Pulsar 文档翻译术语库](https://shimo.im/sheets/5jozGy5WIUQQf5JV/MODOC)，你也可以更新此术语库。

### 提交 Pull Request

翻译完成后，译者需在 snp-cn repo 中提交 Pull Request。建议在提交 PR 后先自行检查一遍，确认无误后，再找 reviewer 审校。

提交 Pull Request 时的设置如下：
- Reviewers：无需设置，负责人收到你提交的 Pull Request，会安排人员处理。
- Assignees：选择你自己，以便后续接收进度通知。
- Label：选择 `translation`。

### 审阅

Reviewer 审阅完成后，在 PR 中告知译者，并做简单点评反馈。如有需要进一步讨论的内容，可以直接在 PR 中交流。 

审阅无误后，reviewer 会批准并合并该 Pull Request。

## 翻译规范 
本规范主要列出了一些翻译中常见的问题及针对这些问题的建议。请译者在开始翻译前认真阅读，并在翻译过程中尽量遵守本规范，有效提高 StreamNative 翻译文件的质量，统一翻译风格。

### 1. 专有名词

- StreamNative Platform、Pulsar、Pulsar Functions、BookKeeper、ZooKeeper，按照上述大小写格式保留英文，不翻译。  
- broker、bookie、proxy 等，保留英文小写形式，不翻译。  
- topic、producer、consumer 等，翻译成“主题”，“生产者”，“消费者”。   

更多 Pulsar 术语的中文翻译，请参考 [Pulsar 文档翻译术语库](https://shimo.im/sheets/5jozGy5WIUQQf5JV/MODOC)，你也可以更新此术语库。

### 2. 汉字与英文、数字之间需空一格

汉字与英文或数字之间以空格隔开，以增强中英文混排的美观性和可读性。 
 
- Apache Pulsar是一个开源的分布式发布-订阅消息系统，由Apache软件基金会管理，并于2018年9月成为Apache顶级开源项目。❌ 

- Apache Pulsar 是一个开源的分布式发布-订阅消息系统，由 Apache 软件基金会管理，并于 2018 年 9 月成为 Apache 顶级开源项目。✅ 

**注意：** 中文标点符号前后不需要添加空格，即使此标点之前或/和之后的内容为英文单词。

###  3. 中文标点符号

中文里面请使用中文标点符号。英文中没有顿号，应该使用顿号的地方在英文中一般使用的是逗号，在翻译时应注意将其翻译为顿号。比如 “Kubernetes, Mesos, Docker“ 应该翻译成 “Kubernetes、Mesos、Docker“。

在涉及到多个条目时，如果每个条目是一个短语，则应使用分号（；）结束，最后一个条目以句号（。）结尾。

如果每个条目是一个句子，或者已经使用过分号（；），则每个条目以句号结尾。

### 4. “you“翻译为“你“

为了风格统一，在翻译“you”时，尽量省略不用；必须使用时，用“你”而不是“您”。

### 5. 示例代码及注释

示例代码中的注释最好能够翻译，当然也可以不翻译（当注释很简单时）。

### 6. 意译优于直译

翻译时尽量避免翻译腔，即把原文一字不漏地、逐句翻译出来。这样并不代表翻译更准确，有时候读着会很别扭。翻译完后，自己多读几遍，看看是否符合原意、是否绕口、是否简洁。在充分理解原文的基础上，可以适当采用意译的方式来翻译。有时候为了简化句子，有些数量词、助词、主语都可以省略。

#### 6.1 ...的...的...的...

比如英文中经常会用 ‘s 来表达从属关系，一字不落地都将其翻译成 “的“ 会有翻译腔。在中文里面有些 “的“ 完全可以去掉，不会影响表达的意思，还能简洁很多，看下面的例子。

- Pulsar‘s documentation is located in the docs folder in Pulsar’s source code repository.

当然，你可以将“的“字都翻译出来，但是读起来很不顺畅：

- Pulsar 的文档位于 Pulsar 的源码仓库的 docs 文件夹中。 ❌ 

去掉不必要的“的“字，就会简洁很多：

- Pulsar 文档位于 Pulsar 源码仓库的 docs 文件夹中。 ✅ 

#### 6.2 一个……

英文一般比较严谨，当代表多个时，会使用复数名字，在翻译成中文时，一般不需要刻意把这些复数翻译出来。

在英文里，当单数为可数名词时，前面一般会有 ”a“ 或 ”an“；中文中，我们一般不需要把”一个...“翻译出来。比如下面这个例子：

- Using a Pulsar client with the proxy

我们可以把”a“翻译成”一个“：

- 使用有 proxy 的一个 Pulsar 客户端   ❌  

虽然看起来没什么问题，但是这里的”一个“是多余的，去掉后不但不会更改原文含义，还使译文更加简洁。

- 使用有 proxy 的 Pulsar 客户端   ✅ 

### 7. 尽量少用被动句

当英文中以物做主语时，中文译文中不应出现”被“字。例如：  

 - 英文：Once the message above has been successfully published to the topic, you should see it in the standard output: Hello Pulsar.
 - 原翻译：一旦上面的消息被成功发布到Topic中，您会在标准输出中看到它：Hello Pulsar”。   ❌
 - 翻译调整：上面的消息成功发布到主题后，你会在标准输出中看到: Hello Pulsar。    ✅ 

### 8. Will 将来时态
英文中有“will”时，不用翻译成“将要......”，直接转化成一般现在时即可。例如：  

- This will publish a single message to the Pulsar topic. 
- 原翻译：这将向Pulsar的Topic发送单条消息。   ❌
- 翻译调整：（......）给 Pulsar 主题发布一条信息。   ✅ 

### 9. 示例
在英文中遇到 “Here‘s sth”，根据上下文语境做调整。例如： 

- Here's an example.
- 原翻译：这里有一个示例。   ❌
- 翻译调整：下面是一个示例。     ✅ 

### 10. 语言简洁、流畅
语言尽量简单、通顺，应适合中国人的阅读习惯。英语中有时会用从句，译成中文时，应尽量拆成几个加单句。

## 加入贡献者名单
当你提交的 Pull Request 合并后，欢迎你将自己的名字添加到[贡献者名单](CONTRIBUTORS.md)。
