# KET TalkPong PRD (v1.1)

## 1. 业务背景

### 1.1. 项目缘起

本项目服务于一名名叫"石榴"的10岁女孩，她生活在杭州，就读于星洲小学，正在备考剑桥英语一级证书（A2 Key，简称 KET）。根据对其现有口语水平的系统性评估，她的英语能力大致介于 CEFR A1（入门级）至 A2（初级）之间，具备基本的沟通意愿和初步的英语交流能力，但在语法准确性（时态、主谓一致、名词复数）和词汇多样性方面存在明显短板，是冲击 KET 考试需要重点突破的环节。

石榴目前在 VIPKid 平台每周与一位英格兰农场主老师进行25分钟的口语对话练习，课外的自主练习机会相对有限。为了在两次正式课程之间保持高频的语言输出，同时维持孩子的学习兴趣，"KET TalkPong" 应运而生。

### 1.2. 产品定位

"KET TalkPong" 是一款**面向 KET 备考阶段小学生的 AI 英语口语陪练应用**。其核心设计理念基于二语习得（SLA）理论，将练习过程游戏化、情境化，构建一个高效、有趣且可持续的个性化备考闭环。

产品名称"TalkPong"取自乒乓球（Ping-Pong）的意象，寓意 AI 与孩子之间像打乒乓球一样你来我往、节奏轻快的对话交互，强调互动性和趣味性。

### 1.3. 核心设计理念

本产品的设计基于以下两个关键洞察：

**"对话"解决敢说的问题，"图片复述"解决说得有逻辑、有内容的问题。** 具体而言：

其一，自由对话模式基于 Krashen 的**输入假设（Input Hypothesis）**与 Swain 的**输出假设（Output Hypothesis）**。AI Agent 在对话中提供略高于孩子当前水平的语言输入（i+1），同时通过开放性问题强制孩子进行语言产出，在无压力的环境中建立表达自信。

其二，图片复述模式基于教学法中的**视觉脚手架理论（Visual Scaffolding）**。由对话内容生成的图片将抽象的语言逻辑具象化，降低认知负荷，使孩子能更专注于语言的组织与表达，同时高度契合 KET 口语考试第二部分（Part 2）的图片描述与讨论环节。

## 2. 需求描述

### 2.1. 核心概念

为确保理解一致，明确以下核心概念：

| 概念 | 描述 |
| :--- | :--- |
| **TalkPong** | 每日的核心练习单元，包含一次完整的"自由对话"与"图片复述"两个阶段，是产品的最小可交付练习体。 |
| **盲盒话题 (Blind Box Topic)** | 当用户没有想聊的话题时，系统从 KET 历年真题库中随机抽取3个话题供选择，在保留自主性的同时确保练习的考试相关性。 |
| **视觉脚手架 (Visual Scaffolding)** | 基于对话内容由 AI 生成的一张总结性图片。该图片作为认知支撑，帮助用户降低组织语言的心理负担，专注于描述和评价。 |
| **三步走纠错法** | 一种温和的纠错策略，分三个阶段进行：① 对话中不纠错，保持流利度；② 对话结束后温和指出1-2个典型语法错误；③ 图片复述阶段引导用户主动应用正确表达。 |
| **成就勋章 (Achievement Medal)** | 每次 TalkPong 结束后自动生成的总结报告卡片，包含"今日突破"、"词汇卡"和"语法提分"三个模块，用于正向激励和知识沉淀。 |
| **长期记忆 (Long-term Memory)** | 系统持久化存储的用户个人信息，包括昵称、年龄、兴趣爱好（滑板、篮球、吉他）、近期经历（北大湖滑雪、长春冰雪新世界）等，用于在每次交互中保持亲切感和个性化。 |
| **KET 真题话题库** | 系统内置的话题数据集，均源自 KET 历年真题的口语考试话题，涵盖家庭、学校、爱好、旅行、食物、朋友等主题。 |

### 2.2. 产品动线设计

下图展示了完整的用户交互动线，从进入应用到完成每日 TalkPong 并获得成就勋章的全流程：

![KET TalkPong 交互动线](https://files.manuscdn.com/user_upload_by_module/session_file/309917789672469614/lOnKzIswdqcKNEdw.jpg)

整体动线可概括为以下五个阶段：**① 进入每日列表 → ② 开始对话（含话题选择）→ ③ 连续对话 → ④ 图片生成与复述 → ⑤ 生成并查看成就勋章**。已完成的 TalkPong 可直接跳转至成就勋章查看页。

### 2.3. 模型设计（Database Schema）

技术层面采用 Drizzle ORM + SQLite (本地文件模式)。为了简化初始部署（目标为阿里云 ECS），数据库将采用本地 SQLite 文件模式 (`file:local.db`)，无需依赖任何独立的数据库服务进程。以下为各数据表的字段定义。

#### 2.3.1. `User` - 用户档案

存储孩子的核心档案信息，作为 AI Agent 的长期记忆基础。

| 字段名 | 数据类型 | 主键 | 说明 |
| :--- | :--- | :--- | :--- |
| `id` | `INTEGER` | 是 | 用户唯一标识，固定为 1 |
| `long_term_memory` | `JSON` | | 结构化的长期记忆，存储动态更新的个人信息，便于 Agent 理解和维护 |
| `created_at` | `DATETIME` | | 创建时间 |

**`long_term_memory` 字段 JSON 结构示例**：

```json
{
  "profile": {
    "name": "石榴",
    "age": 10,
    "city": "杭州",
    "school": "星洲小学"
  },
  "language_level": {
    "current_cefr": "A1-A2",
    "goal": "A2 Key (KET)",
    "strengths": "积极的沟通意愿，能够围绕熟悉话题展开多回合对话。",
    "weaknesses": {
      "fluency": "表达中存在较多停顿和犹豫。",
      "vocabulary": "词汇量有限，存在重复使用和误用情况。",
      "grammar": "时态混淆、主谓一致、名词单复数是主要弱项。"
    }
  },
  "interests_and_activities": {
    "hobbies": ["滑板", "篮球", "吉他"],
    "recent_events": [
      {
        "event": "去北大湖滑雪和长春冰雪新世界玩",
        "date": "最近"
      }
    ]
  },
  "learning_preferences": {
    "weekly_practice": "每周与 vipkid 的英格兰农场主老师对话25分钟。",
    "interaction_style": "需要多鼓励，喜欢有成就感。"
  }
}
```

#### 2.3.2. 对话会话（TalkSession）

记录每一次完整的 TalkPong 练习，是所有子记录的父实体。

| 字段名 | 类型 | 描述及约束 |
| :--- | :--- | :--- |
| `id` | String | 会话唯一 ID，主键，使用 CUID 生成。 |
| `user_id` | String | 关联的用户 ID，外键。 |
| `topic` | String | 本次对话的主题，可由用户自定义或从盲盒话题中选取。 |
| `topic_source` | Enum | 话题来源：`USER_DEFINED`（用户自定义）或 `BLIND_BOX`（盲盒话题）。 |
| `start_time` | DateTime | 会话开始时间。 |
| `end_time` | DateTime | 会话结束时间，完成后更新。 |
| `generated_image_url` | String | 基于对话内容生成的视觉脚手架图片 URL，图片生成前为空。 |
| `image_prompt` | Text | 发送给图像生成 API 的提示词，用于调试和记录。 |
| `status` | Enum | 会话状态：`PENDING`（待开始）、`IN_DIALOGUE`（对话中）、`IN_RETELLING`（复述中）、`COMPLETED`（已完成）。 |
| `created_at` | DateTime | 记录创建时间。 |

#### 2.3.3. 对话记录（DialogueTurn）

记录对话中的每一轮交互，是生成成就勋章和图片提示词的核心数据来源。

| 字段名 | 类型 | 描述及约束 |
| :--- | :--- | :--- |
| `id` | String | 记录唯一 ID，主键，使用 CUID 生成。 |
| `session_id` | String | 关联的会话 ID，外键。 |
| `phase` | Enum | 所属阶段：`DIALOGUE`（自由对话阶段）或 `RETELLING`（图片复述阶段）。 |
| `speaker` | Enum | 发言者：`USER` 或 `AI`。 |
| `text` | Text | 发言的文本内容。 |
| `timestamp` | DateTime | 发言时间戳。 |
| `sequence` | Integer | 本轮在会话中的顺序编号，从1开始递增。 |

#### 2.3.4. 成就勋章（AchievementMedal）

记录每次练习后由 AI 生成的总结勋章，是激励系统的核心数据实体。

| 字段名 | 类型 | 描述及约束 |
| :--- | :--- | :--- |
| `id` | String | 勋章唯一 ID，主键，使用 CUID 生成。 |
| `session_id` | String | 关联的会话 ID，外键，唯一约束（一次会话对应一枚勋章）。 |
| `user_id` | String | 关联的用户 ID，外键，用于快速查询用户的历史勋章。 |
| `breakthrough` | Text | "今日突破"模块内容：一段鼓励性评语，表扬本次练习的亮点。 |
| `vocab_cards` | JSON/Text | "词汇卡"模块内容：以 JSON 格式存储2-3个本次学到的词汇，包含词汇、释义和例句。 |
| `grammar_correction` | JSON/Text | "语法提分"模块内容：以 JSON 格式存储1-2个典型语法错误的对比（错误句 vs. 正确句）及简要解释。 |
| `created_at` | DateTime | 勋章生成时间。 |

**`vocab_cards` 字段示例：**
```json
[
  {
    "word": "sculpture",
    "definition": "A piece of art made by shaping stone, wood, or other materials.",
    "example": "There were many beautiful ice sculptures at the festival."
  }
]
```

**`grammar_correction` 字段示例：**
```json
[
  {
    "error": "I go to the snow slide yesterday.",
    "correction": "I went to the snow slide yesterday.",
    "explanation": "When talking about something that happened in the past, we use the past tense. 'go' → 'went'."
  }
]
```

#### 2.3.5. KET 话题库（KetTopic）

存储内置的 KET 真题话题，用于"盲盒话题"功能的随机抽取。

| 字段名 | 类型 | 描述及约束 |
| :--- | :--- | :--- |
| `id` | String | 话题唯一 ID，主键。 |
| `topic_text` | String | 话题描述，如"Tell me about your favourite sport."。 |
| `category` | String | 话题分类，如"Sports"、"School"、"Family"、"Hobbies"。 |
| `source` | String | 来源说明，如"KET 2023 Paper 1"。 |

**KetTopic 初始化数据示例（部分）：**
```json
[
  { "id": "kt001", "topic_text": "Tell me about your favourite sport.", "category": "Sports", "source": "KET 2022" },
  { "id": "kt002", "topic_text": "What do you like to do after school?", "category": "Hobbies", "source": "KET 2021" },
  { "id": "kt003", "topic_text": "Tell me about a place you visited recently.", "category": "Travel", "source": "KET 2023" },
  { "id": "kt004", "topic_text": "What is your favourite food? Why?", "category": "Food", "source": "KET 2020" },
  { "id": "kt005", "topic_text": "Tell me about your best friend.", "category": "Friends", "source": "KET 2022" },
  { "id": "kt006", "topic_text": "What do you do at the weekends?", "category": "Daily Life", "source": "KET 2021" },
  { "id": "kt007", "topic_text": "Tell me about your school.", "category": "School", "source": "KET 2023" },
  { "id": "kt008", "topic_text": "What kind of music do you like?", "category": "Hobbies", "source": "KET 2020" }
]
```

### 2.4. 功能描述

#### 2.4.1. 每日 TalkPong 列表（首页）

**功能描述**：应用的主入口，展示当日及历史的 TalkPong 记录，是用户每日开始练习的起点。

**业务流程**：

| 步骤 | 业务逻辑 | 异常处理 |
| :--- | :--- | :--- |
| 1. 进入首页 | 系统加载当前用户的 TalkPong 列表，按日期倒序排列。 | 数据加载失败则显示重试提示。 |
| 2. 展示当日任务 | 若当日尚无 TalkPong 记录，系统自动创建一条状态为 `PENDING` 的记录，并在列表顶部突出显示"今日 TalkPong"。 | — |
| 3. 状态区分展示 | 列表中每条记录根据 `status` 字段显示不同状态：`PENDING` 显示"待开始"并附开始按钮；`COMPLETED` 显示"已完成"并附勋章摘要（`breakthrough` 字段的前30字）。 | — |
| 4. 跳转入口 | 点击"待开始"记录，进入对话流程（2.4.2）；点击"已完成"记录，进入成就勋章查看页（2.4.4）。 | — |

#### 2.4.2. TalkPong 对话流程

**功能描述**：TalkPong 的核心交互环节，包含话题选择、自由对话和对话收尾三个子阶段，全程遵循"三步走纠错法"的第一步和第二步。

**业务流程**：

| 步骤 | 业务逻辑 | 异常处理 |
| :--- | :--- | :--- |
| 1. 进入对话 | 会话状态更新为 `IN_DIALOGUE`。AI 以亲切的方式问候用户（使用昵称"石榴"），并询问今天想聊什么话题。 | — |
| 2. 话题选择 | 若用户提出话题，直接进入步骤3；若用户表示没有想法（如"I don't know"或"随便"），系统从 `KetTopic` 表中随机抽取3条记录，以卡片形式展示供用户选择。 | 数据库查询失败则使用预设的3个备用话题。 |
| 3. 连续对话 | AI 根据用户选定的话题展开对话，遵循以下原则：① **不纠错**：对话中不打断、不纠正任何语法错误；② **i+1 输入**：使用略高于 A1-A2 水平的词汇和句式；③ **鼓励引导**：使用"That's great!"、"Tell me more!"等正向反馈，并通过开放性问题引导用户多说；④ **个性化**：调用用户的 `long_term_memory`，将对话与用户的兴趣和经历自然关联。每轮对话记录存入 `DialogueTurn` 表（`phase: DIALOGUE`）。 | LLM API 调用失败则重试最多2次，仍失败则提示用户稍后重试。 |
| 4. 对话收尾 | 当对话进行约8-12轮，或用户表达结束意愿时，AI 温和地结束对话，并告知将进入图片生成环节。此时，AI 根据整个对话记录，挑选1-2个最典型的语法错误，以温和、鼓励的方式向用户指出（"三步走纠错法"第二步），并给出正确表达。 | — |
| 5. 触发图片生成 | 后台根据对话记录（`DialogueTurn` 表中 `phase: DIALOGUE` 的所有记录），调用 LLM API 生成图片描述提示词（`image_prompt`），再调用图像生成 API 生成图片，将图片 URL 更新至 `TalkSession.generated_image_url`。 | 图片生成失败则重试，仍失败则跳过图片环节，直接进入文字复述。 |

#### 2.4.3. 图片复述流程

**功能描述**：展示由对话内容生成的视觉脚手架图片，引导用户进行图片描述和喜好表达，对标 KET 口语 Part 2 的考试场景。

**业务流程**：

| 步骤 | 业务逻辑 | 异常处理 |
| :--- | :--- | :--- |
| 1. 展示图片 | 会话状态更新为 `IN_RETELLING`。前端展示生成的图片，AI 给出任务指令："Here is a picture about what we just talked about. Can you describe what you see? And tell me what you like about it!" | — |
| 2. 用户复述 | 用户对图片进行描述和评价。AI 在此阶段可以：① 对用户的描述进行追问，如"What are they doing here?"、"Why do you like this part?"；② 若用户在复述中重复了对话阶段已指出的语法错误，在用户说完后以"A better way to say it is..."进行温和提醒（"三步走纠错法"第三步）。每轮复述记录存入 `DialogueTurn` 表（`phase: RETELLING`）。 | — |
| 3. 复述收尾 | 经过约4-6轮图片描述互动后，AI 给予热情的鼓励，宣布本次 TalkPong 圆满完成，并告知将生成今日的成就勋章。 | — |
| 4. 触发勋章生成 | 后台汇总本次会话的所有 `DialogueTurn` 记录，调用 LLM API 生成成就勋章的三个模块内容，存入 `AchievementMedal` 表，会话状态更新为 `COMPLETED`。 | LLM API 调用失败则重试，仍失败则生成一个简化版勋章（仅含鼓励语）。 |

#### 2.4.4. 成就勋章查看

**功能描述**：以视觉化卡片的形式展示本次 TalkPong 的学习成果，是激励系统的核心呈现界面。

**业务流程**：

| 步骤 | 业务逻辑 | 异常处理 |
| :--- | :--- | :--- |
| 1. 进入勋章页 | 从首页列表点击"已完成"的 TalkPong，或在复述流程结束后自动跳转至此页面。 | — |
| 2. 展示勋章内容 | 页面以卡片布局展示三个模块：① **今日突破**：以大字体、鼓励性色彩展示 `breakthrough` 字段内容；② **词汇卡**：以翻卡（Flip Card）或列表形式展示 `vocab_cards` 中的词汇、释义和例句；③ **语法提分**：以"错误句 → 正确句"的对比格式展示 `grammar_correction` 内容，错误句用红色标注，正确句用绿色标注。 | — |
| 3. 历史回顾 | 用户可从首页列表进入任意历史 TalkPong 的勋章页，回顾过往学习记录。 | — |

### 2.5. AI Agent 行为规范

AI Agent 的行为是产品体验的核心，需严格遵循以下规范，确保符合儿童用户的心理特征和 KET 备考目标。

#### 2.5.1. 角色设定

AI Agent 扮演一位**友善、耐心、充满活力的英语学习伙伴**，而非严肃的老师或考官。它了解石榴的所有个人信息，能够像老朋友一样自然地聊天，并在适当的时候将话题引向 KET 考试相关的语言点。

#### 2.5.2. 语言风格

AI Agent 在对话中使用的语言须满足以下要求：

| 维度 | 规范 |
| :--- | :--- |
| **词汇难度** | 以 A1-A2 词汇为主，适当引入 A2-B1 词汇（即 i+1 输入），避免使用超出 B1 的复杂词汇。 |
| **句式复杂度** | 以简单句和常见复合句为主，避免复杂从句。 |
| **语气** | 始终保持积极、鼓励、轻松的语气，多使用感叹句和疑问句调动气氛。 |
| **中文使用** | 对话全程使用英语，但若用户用中文表达困惑，AI 可用中文简短解释后，立即切换回英语继续对话。 |
| **个性化** | 每次对话开始时必须使用"石榴"的昵称，并在对话中自然融入其兴趣和经历。 |

#### 2.5.3. 纠错规范（三步走纠错法详细说明）

| 阶段 | 纠错行为 | 示例 |
| :--- | :--- | :--- |
| **对话阶段** | **绝对不纠错**。即使用户出现明显的语法错误，AI 也只需理解其意思并自然回应，不做任何形式的纠正或重复正确形式。 | 用户："I go to the ice world yesterday." AI："Oh, that sounds so fun! What did you see there?" |
| **对话收尾** | 从整个对话中挑选**最典型的1-2个**语法错误，以温和、鼓励的方式指出，给出正确表达。 | "By the way, I noticed something small — when we talk about yesterday, we say 'I **went** to the ice world', not 'I go'. You're doing great, just a tiny thing to remember!" |
| **图片复述** | 若用户在复述中再次出现已纠正的错误，在用户说完后以"A better way to say it is..."进行提醒，鼓励其在接下来的表达中尝试使用正确形式。 | "A better way to say it is 'I **went** there'. Can you try saying that again?" |

### 2.6. 外部 API 调用

本产品将全面采用阿里云通义千问（Qwen）系列模型，通过其百炼平台提供的 API 进行调用。所有 API Key 均作为服务端环境变量进行管理。

#### 2.6.1. LLM 对话 API (Qwen-Chat)

**接口**：采用兼容 OpenAI 的 SDK 模式进行调用。
- **Endpoint**: `https://dashscope.aliyuncs.com/compatible-mode/v1`
- **HTTP 请求地址**: `POST https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions`

**调用参数**：

| 参数 | 推荐值 | 说明 |
| :--- | :--- | :--- |
| `model` | `qwen-plus` | 千问增强版模型，综合性能较好。 |
| `messages` | (动态生成) | 对话上下文，包含 `system` 和 `user` 角色的消息。 |
| `stream` | `true` | 必须采用流式输出 (`stream: true`)，以保证对话的实时性和用户体验。 |

**核心 System Prompt 策略**：

| 用途 | 核心 System Prompt 策略 | 关键输入 |
| :--- | :--- | :--- |
| **对话交互** | "你是石榴的英语学习伙伴，了解她的所有个人信息（见 `long_term_memory`）。对话全程用英语，语言难度控制在 A1-A2 级别。对话中绝对不纠错，只需鼓励和引导。" | `long_term_memory`、当前话题、历史对话记录（`DialogueTurn` 列表） |
| **图片提示词生成** | "根据以下对话内容，生成一段简洁的英文图片描述，用于生成一张色彩鲜明、风格可爱的儿童插画。描述应包含主要人物、场景、物品和动作，不超过100词。" | 对话阶段的所有 `DialogueTurn` 记录 |
| **成就勋章生成** | "根据以下对话记录，生成一枚成就勋章，包含三部分：① breakthrough（鼓励性评语，中文，50字以内）；② vocab_cards（2-3个词汇，JSON数组）；③ grammar_correction（1-2个典型错误对比，JSON数组）。严格按 JSON 格式输出。" | 本次会话的所有 `DialogueTurn` 记录 |

#### 2.6.2. 文本到图像生成 API (Qwen-Image)

**图片生成耗时处理逻辑**：

由于文生图耗时较长（通常需要 5-10 秒），为避免用户在空白界面前焦虑等待，需设计明确的前后端处理逻辑：

1.  **前端触发**：当结束对话，进入“图片复述”环节时，前端立即显示一个加载状态的占位图。
    -   **UI 表现**：占位图上应有动态效果（如加载动画、进度条）和提示性文字，例如：“AI 正在为你画画，请稍等... 🎨”。
2.  **后端异步处理**：后端接收到生成请求后，应立即返回一个“任务已接收”的响应，并开始异步调用 Qwen-Image API。
3.  **前端轮询**：前端在收到“任务已接收”响应后，开始以固定间隔（如每 2 秒）向后端轮询图片生成结果。
4.  **后端返回结果**：图片生成成功后，后端将图片 URL 存入数据库，并在下次轮询时返回给前端。
5.  **前端渲染**：前端获取到图片 URL 后，停止轮询，并将图片渲染到页面上，替换原有的加载占位图，正式开始图片复述环节。

**接口**：采用原生 HTTP 请求模式调用。
- **HTTP 请求地址**: `POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`

**核心请求体 (Body) 结构**：
```json
{
  "model": "qwen-image-max",
  "input": {
    "messages": [
      {
        "role": "user",
        "content": [
          { "text": "(由 LLM 生成的图片提示词)" }
        ]
      }
    ]
  },
  "parameters": {
    "negative_prompt": "low resolution, bad quality, malformed limbs, malformed fingers, oversaturated, waxiness, no details on face, over-smoothing, AI-like feeling, cluttered composition, blurry text, distorted text",
    "size": "1024*1024"
  }
}
```

#### 2.6.3. 语音合成 API (Qwen-TTS)

**接口**：采用原生 HTTP 请求模式调用，并开启流式传输。
- **HTTP 请求地址**: `POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`
- **HTTP Header**: 必须包含 `X-DashScope-SSE: enable` 以启用流式输出，确保音频能够实时播放，降低延迟感。

**接口**：采用原生 HTTP 请求模式调用，并开启流式传输。
- **HTTP 请求地址**: `POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`
- **HTTP Header**: 必须包含 `X-DashScope-SSE: enable` 以启用流式输出。

**核心请求体 (Body) 结构**：
```json
{
  "model": "qwen3-tts-flash",
  "input": {
    "text": "(AI Agent 的对话文本)",
    "voice": "Cherry",
    "language_type": "English"
  }
}
```
**说明**：AI Agent 的每一句英文回应，都将通过此 API 实时合成为语音，并以流式音频的方式在前端播放，实现“边说边听”的效果，增强交互的真实感和沉浸感。`voice` 参数可根据效果选择，`Cherry` 为推荐的甜美童声。

## 3. 技术架构

### 3.1. 技术栈

| 层级 | 技术选型 | 说明 |
| :--- | :--- | :--- |
| **统一框架** | Next.js 15（App Router） | 前后端统一，支持 SSR/SSG，优化首屏加载速度；Route Handlers 承担 API 层职责。 |
| **编程语言** | TypeScript | 全栈类型安全，减少运行时错误，提升代码可维护性。 |
| **样式方案** | Tailwind CSS 4 | 原子化 CSS，快速实现响应式设计，无需额外 CSS 文件。 |
| **ORM** | Drizzle ORM | 类型安全的 SQL ORM，与 TypeScript 深度集成，Schema 即类型定义。 |
| **数据库** | Turso (libsql) | 本地开发使用 `file:local.db`（SQLite），生产环境使用 Turso 云数据库，零配置迁移。 |
| **AI 接口** | OpenAI SDK | 统一调用 LLM 对话（流式输出）和图像生成接口。 |
| **部署平台** | Vercel | 与 Next.js 原生集成，支持边缘函数，自动 CI/CD。 |

### 3.2. 响应式设计原则

本产品采用**移动优先（Mobile First）**的设计策略，一套代码库同时适配手机端和 Web 端：

- **手机端**（主要使用场景）：单列布局，大号触控按钮（最小点击区域 44×44px），对话气泡式 UI，底部固定输入框。
- **Web 端**（辅助使用场景）：双列布局（左侧对话区，右侧图片/勋章区），键盘快捷键支持（Enter 发送消息）。
- **断点策略**：以 Tailwind CSS 4 的 `sm`（640px）为核心断点，区分手机端和 Web 端布局。

### 3.3. 关键页面路由

| 路由 | 页面名称 | 说明 |
| :--- | :--- | :--- |
| `/` | 每日 TalkPong 列表（首页） | 展示当日及历史练习记录，是用户的主入口。 |
| `/session/[id]` | TalkPong 对话与复述页 | 对话流程的核心交互页面，根据 `TalkSession.status` 动态切换对话/复述/完成状态。 |
| `/session/[id]/medal` | 成就勋章查看页 | 展示指定会话的成就勋章卡片，支持历史回顾。 |
| `/api/chat` | 对话 API 路由 | Next.js Route Handler，处理 LLM 对话请求，支持流式输出（Streaming）。 |
| `/api/generate-image` | 图片生成 API 路由 | Next.js Route Handler，处理图片生成请求，返回图片 URL。 |
| `/api/generate-medal` | 勋章生成 API 路由 | Next.js Route Handler，处理成就勋章生成请求，返回结构化 JSON。 |

### 3.4. 数据流示意

一次完整 TalkPong 的数据流如下：

```
用户输入
  → /api/chat（LLM 流式输出）
  → DialogueTurn 记录写入
  → [对话结束] /api/generate-image（图片生成）
  → TalkSession.generated_image_url 更新
  → [复述结束] /api/generate-medal（勋章生成）
  → AchievementMedal 记录写入
  → TalkSession.status 更新为 COMPLETED
  → 前端跳转至勋章页
```

## 4. 非功能性需求

| 类别 | 需求描述 |
| :--- | :--- |
| **性能** | 对话接口响应时间（首 Token）应在3秒以内；图片生成时间应在15秒以内，期间展示加载动画。 |
| **可用性** | 界面导航文字以中文为主，对话内容全程英文；操作步骤不超过3步，降低使用门槛。 |
| **安全性** | API Key 存储于服务端环境变量（`.env.local`），不暴露于前端；用户数据本地存储，不上传至第三方。 |
| **可扩展性** | 数据模型预留多用户支持能力，未来可扩展为家庭多账号或班级使用场景；KET 话题库支持后台动态增减。 |
| **儿童友好性** | 界面色彩明亮活泼，字体较大（正文不小于16px），交互操作简单直观，避免复杂的设置和配置项。 |

## 5. 附录：石榴用户档案（系统初始化数据）

以下信息将作为系统初始化数据写入 `User` 表，是 AI Agent 保持个性化交互的核心依据，务必在系统初始化时完整录入。

| 字段 | 内容 |
| :--- | :--- |
| **昵称** | 石榴 |
| **年龄** | 10岁 |
| **性别** | xx |
| **城市** | xx |
| **学校** | xx |
| **当前兴趣** | xx、xx、xx |
| **近期经历** | xx、xx |
| **英语学习背景** | xx |
| **当前 CEFR 水平** | A1-A2（入门至初级） |
| **备考目标** | 剑桥英语一级证书（A2 Key，KET） |
| **性格特点** | 积极主动，有好奇心，喜欢主动提问，具备良好的互动意识 |
| **主要语法弱项** | 时态混淆（惯用现在时描述过去）、主谓一致（第三人称单数）、名词单复数 |
| **主要词汇弱项** | 词汇量有限，存在重复使用（如 "a lot of", "very"）和误用（如将 "sculpture" 说成 "schedule"）的情况 |
