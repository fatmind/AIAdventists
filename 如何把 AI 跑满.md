# 如何把 AI 跑满

"没有能力消耗 token，未来就是废物。"这话刺耳，但说的是事实：**把 AI 跑满**，正在成为个人和企业的关键能力。

## 一、Token 消耗百倍增长：从"问答"到"工作流"

Token 消耗增长 100 倍，原因有两个：**用的人多了**，**每次任务用的量也多了**。

用户增长很直观。OpenAI 的数据显示，企业客户的周消息量一年翻了 8 倍 [1]。但更重要的变化是单次任务的消耗量。我们正从"一问一答"，转向 AI 自主完成复杂任务的"代理式工作流"。

a16z 的报告指出，增长最快的 AI 用法是"代理式推理" [2]。这种模式下，AI 自己规划、调用工具、查资料、反复迭代，直到完成目标。Token 消耗远超写一篇文章。OpenAI 称，机构用户的"推理 Token"消耗在 12 个月里增长了 **320 倍** [1]。

| 交互模式 | 说明 | Token 用量 | 典型应用 |
| :--- | :--- | :--- | :--- |
| 问答式 | 你提问，AI 回答 | 较少 | 知识问答、内容生成 |
| 代理式工作流 | 你定目标，AI 自己搞定多步任务 | 很多 | 自动化开发、市场研究 |

## 二、为什么你的 AI 跑不满？

很多人"200 美金用不满"。问题不在模型，而在我们驾驭 AI 做复杂事的能力和配套设施。

### 瓶颈一：搞不定"长时任务"

AI 目前最大的短板，是没法独立完成需要长时间、跨多个会话的复杂任务（Long-running Tasks）。这跟模型能力有关，但更关键的是我们缺乏驾驭它的方法。

Anthropic 的工程师发现，AI 每次新会话都像个"失忆工程师"——忘了之前干了什么。结果要么想一步到位、半路崩掉，要么到了后期误以为任务已经完成 [4]。

想突破这个瓶颈，两件事值得做：

**a) 跟着 Anthropic 走一遍长时任务管理：** 他们设计了一套"harnesses"，思路是模仿好的软件工程师干活。具体来说：先让一个"初始化代理"把大任务拆成详细的功能点清单（比如一个 JSON 文件），搭好测试环境。然后让"编码代理"每次会话只做一个功能点，做完 git commit 并记录进度。这样每个新"失忆工程师"（新会话）都能通过清单、代码历史和进度文件接手工作，持续推进。亲手跑一遍，你会对"如何管理 AI 做大项目"有完全不同的理解。

**b) OpenClaw，在你电脑上跑得更好：** OpenClaw 最近很火，它通过本地文件访问、持久化记忆和工具集成，给 AI 代理搭了个能跨越上下文窗口的脚手架 [5]。在自己电脑上跑起来，不光能体验 24/7 不停歇的 AI 助理，更重要的是理解它怎么管记忆、怎么跟本地应用交互、怎么学新技能。搞明白原理，你才能把它用得更好。

### 瓶颈二：没有围绕 AI 建立你的基础设施

要真正跑满 AI，得给它搭一套它能高效使用的基础设施。

如果你有以下情况，说明你的基础设施，还没建立起来：

- **重复造轮子**：遇到类似问题，重新写代码或 Prompt，没有封装成 AI 能调用的 **CLI 工具 **或 **Skill**。
- **查 API 用法**：调阿里云或某个模型的 API，每次翻半天文档，因为参数复杂记不住。好的做法是把常用 API 封装成简单函数。
- **代码没自验证**：AI 写的代码还得你手动跑测试。高效流程应该让 AI 自己测、自己找错、自己修。

给 AI 搭基础设施，是你得主动去适应 AI。

## 三、为 AI 搭建个人"操作系统"

把 AI 模型当成新"CPU"，我们也需要一个能把它跑满的操作系统。不是 Windows 或 macOS，而是围绕**数据触发**和**能力调用**搭建的框架。下图是一个参考模型：

![AI 操作系统参考图](https://private-us-east-1.manuscdn.com/sessionFile/bTitqCkw7yZsH5UYXrC4aA/sandbox/Iww6xfOaK5zkG6oZ248hiB-images_1771844759270_na1fn_L2hvbWUvdWJ1bnR1L2FpX29zX2RpYWdyYW0.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvYlRpdHFDa3c3eVpzSDVVWVhyQzRhQS9zYW5kYm94L0l3dzZ4Zk9hSzV6a0c2b1oyNDhoaUItaW1hZ2VzXzE3NzE4NDQ3NTkyNzBfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwyRnBYMjl6WDJScFlXZHlZVzAucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzk4NzYxNjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=F2mZNZ9DKIHRsyi4O6Gfl0fJeBLB67q6MKrETiV1MICrIa4f-0ZQ6fY0U9DsuDHM3xBx-cvM6KPIXEbw4AnuMCoLhrV1gufaZtLY3AKGzR8FXYzl4CYTdFGmWYtga5ezvm657mIxHTMHCYR6Lc7oJFzJmXqEjufL7b8mAPx0cf6pMqPLp1F8Ffsw8aRSBoW~TLK9keyP7pPA9kQEGTsqegos8tx3L9FUBSeGGLKeVxx2IDcTVjDiCMKyVtG4d9O4c5ZV4NvQRhpvHOyzF21ZlQD~avp9f-4nUQGwwUzQlZQ1WQxnZIwTeOq5-5eiowDd6Eqtt1jtOzX6ysJRbrzq2Q__)

这个系统靠两点运转：

### 1. 找到 "自动触发点"

系统的输入不是人点鼠标，而是来自真实世界的**新数据**。比如让 4S 店销售每天带录音笔，录下和客户的真实对话。这些过去被忽略线下数据，一旦采集处理，就成了驱动 AI 发挥价值的机会。

### 2. 建立你的基础设施

系统核心是**模型**，但光有大脑不够，还得有手脚——跟世界交互的**工具（Tools）**和解决问题的**经验（Skills）**。

这套设施分层：

- **普适工具与经验**：处理文件、音视频等通用能力，封装为可复用的 Skill。
- **特定工具与经验**：针对你工作场景的 CLI 工具或专用 Skill。比如你常用的云 API 封装、你的代码验证流程。

大公司会占领通用层，但特定场景的工具和经验，是每个人、每个团队自己要建的。

## 四、企业尝试：越过旧系统 OTT

传统企业做数字化转型，总想把 AI 嵌到现有的老旧系统里。这就像是中国去追美国的信用卡，根本追不上，就应该直接跳到数字支付——前面那个是追不上的，而且是大坑，直接跳过去。

拿 4S 店举例。过去想了解销售过程，只能靠 CRM 里销售自己填的记录——高度概括，经常失真。

现在的做法：让销售每天带录音设备，录下数小时的真实对话。AI 自动转文字、分析内容——识别客户关注点，提炼高手话术，找出客户抱怨最多的问题。这些直接用来培训新人、改进产品介绍。

零售公司也一样，每天复盘会佩戴录音设备，产生新数据，挖新机会。

这个流程完全绕开了老旧的 CRM。没增加销售的填报负担，反而给管理层带来了真实、细致的业务洞察。这就是"越顶传球"——**不去改造旧系统，而是用 AI 直接在新场景下创造新数据、搭建新流程。**



>  "把 AI 跑满"是个 关键问题，该动手搭你的 AI 工作台了。



## 参考文献

[1] OpenAI. (2025, December 8). *The state of enterprise AI*. [https://openai.com/index/the-state-of-enterprise-ai-2025-report/](https://openai.com/index/the-state-of-enterprise-ai-2025-report/)
[2] Aubakirova, M., & Midha, A. (2025, December 4). *State of AI: An Empirical 100 Trillion Token Study with OpenRouter*. Andreessen Horowitz. [https://a16z.com/state-of-ai/](https://a16z.com/state-of-ai/)
[3] J.P. Morgan Asset Management. (2026, January 1). *Smothering Heights*. [https://am.jpmorgan.com/content/dam/jpm-am-aem/global/en/insights/eye-on-the-market/smothering-heights-amv.pdf](https://am.jpmorgan.com/content/dam/jpm-am-aem/global/en/insights/eye-on-the-market/smothering-heights-amv.pdf)
[4] Anthropic. (2025, November 26). *Effective harnesses for long-running agents*. [https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
[5] DigitalOcean. (2026, January 30). *What is OpenClaw? Your Open-Source AI Assistant for 2026*. [https://www.digitalocean.com/resources/articles/what-is-openclaw](https://www.digitalocean.com/resources/articles/what-is-openclaw)
[6] Karpathy, A. (2025, June 18). *Software Is Changing (Again)*. Y Combinator. [https://www.ycombinator.com/library/MW-andrej-karpathy-software-is-changing-again](https://www.ycombinator.com/library/MW-andrej-karpathy-software-is-changing-again)
