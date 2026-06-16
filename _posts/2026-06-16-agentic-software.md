---
layout:      post
title:       "软件工程的终结：AI智能体如何从根本上重构软件范式"
title_zh:    "软件工程的终结：AI 智能体如何从根本上重构软件范式"
title_en:    "The End of Software Engineering: How AI Agents Fundamentally Restructure the Software Paradigm"
subtitle_zh: "解读 Agentic Software（arXiv:2606.05608v2）"
subtitle_en: "A reading of Agentic Software (arXiv:2606.05608v2)"
excerpt_zh:  "半个世纪以来，软件工程建立在一个前提之上：人类工程师把决策逻辑写进静态代码。本文论证 AI 智能体的出现从根本上重构了“软件是什么”。"
excerpt_en:  "For over half a century, software engineering rested on one premise: humans encode decision logic into static code. This paper argues AI agents fundamentally restructure what software is."
date:        2026-06-16
author:      Zhenfeng Cao
header-img:  "img/post-bg-universe.jpg"
catalog:     false
categories:  [paper]
tags:
    - 论文
    - Paper
    - AI Agent
    - 软件工程
---

<div class="i18n-zh" markdown="1">

> **原文出处**：Zhenfeng Cao，*Agentic Software: How AI Agents Are Restructuring the Software Paradigm*，arXiv:2606.05608v2 [cs.SE]，2026 年 6 月。本文为中英对照的忠实编译，可用导航栏语言按钮切换英文原文。

## 摘要

半个多世纪以来，软件工程一直运行在一个基础前提之上：由人类工程师分解问题、把决策逻辑编码进静态代码，并随着需求演进手工修改这些代码。本文论证：AI 智能体的出现——一种以大语言模型作为主要推理引擎、把代码当作工具性资源动态生成并丢弃的系统——构成了对“软件是什么”的根本性重构，而非一次渐进式的工具改良。

我们形式化地区分传统的确定性软件与智能体软件：在前者中，代码是预先写好的决策逻辑的载体；在后者中，**智能体本身即软件**，其决策逻辑在运行时生成。我们梳理了从授权软件到 SaaS、再到“智能体即服务”（AaaS）的历史弧线，指出每一次转变都把更多复杂性从终端用户身上转移走——而智能体的转变不仅转移了运营复杂性，更转移了**决策复杂性本身**。

我们提出“智能体工程”（Agentic Engineering），将其视为软件工程学科向新范式的扩展：其研究对象（智能体系统而非静态源码）、控制模型（LLM 驱动而非人类预定义）、以及人的角色（意图架构师而非代码作者）都截然不同。通过对 SWE-bench Verified、EvoClaw、LangChain 多智能体协调研究等近期基准证据的分析，我们既展示了该范式的变革潜力，也指出其当前局限，并以面向自演化智能体生态的四阶段路线图作结。

## 1. 引言

软件工程在 1968 年北约会议上被正式确立，它诞生于一场危机：系统复杂度的增长超出了临时编程实践所能驾驭的范围。该学科的奠基洞见是：严谨的方法论——结构化设计、模块化分解、配置管理、系统化测试——能够驯服这种复杂性。五十年来，这个赌注基本奏效：我们从瀑布走向敏捷，从单体走向微服务，从手工部署走向 CI/CD。

然而，一个更深层的结构性问题始终存在。正如 Brooks 在《人月神话》中所观察的，软件复杂性表现出与其他工程领域根本不同的伸缩行为。软件没有制造环节——设计即产品。每个新特性、每个边界情况、每个集成点，都会增加 Brooks 所称的“本质复杂性”：问题本身固有、而非实现偶然带来的复杂性。

本文主张，AI 智能体的出现并非只是在既有范式内提供了一件新工具，而是从根本上重构了软件工程赖以建立的前提——不是终结它，而是扩展它的定义。AI 智能体本身就是软件：它运行在硬件上、处理数据、产生输出。它的与众不同之处不在于跳出了“软件”这一范畴，而在于它代表了一种**新型软件**——决策逻辑不再预先写好，而是在运行时动态生成。当大语言模型能够理解任务、将其分解为子任务、动态生成代码执行这些子任务、并在不再需要时丢弃这些代码时，代码的角色就从“系统本身”变成了“推理的临时工具”。这一转变，与从模拟电路到存储程序计算机的过渡同样根本。

我们提出三个核心论断：

1. **第一性原理上的必然性。** 智能体范式不是市场偏好，而是复杂性伸缩规律的必然结果。传统软件要求人类工程师显式编码每个决策；基于 LLM 的智能体则可把推理外包给容量随训练算力增长的模型，从而非线性地驾驭复杂性。
2. **软件被重新定义，而非被取代。** 从“AI → 软件 → 结果”到“智能体 → 结果”的转变并不消灭软件——智能体本身就是软件，只是种类根本不同。它坍缩了中间环节：智能体同时是软件系统及其执行引擎，从而免除了对独立的、静态编码产物的需要。
3. **新兴学科。** 智能体工程正作为一种独立实践浮现，拥有自己的概念、工具与度量。其从业者不是“更好的程序员”，而是一种根本不同的角色：意图架构师、智能体协调者、结果审计者。

## 2. 第一性原理分析

### 2.1 传统软件的本质

**定义 2.1（传统软件系统）：** 传统软件系统 S 是一个三元组，其中 C 是计算资源集合（CPU、内存、I/O）；D 是编码在源码中的确定性决策规则集合；E 是把 D 作用于输入以产生输出的执行环境。

<div class="formula"><span class="var">S</span> = (<span class="var">C</span>, <span class="var">D</span>, <span class="var">E</span>)</div>

关键性质在于：**D 相对于执行是静态的**——所有决策逻辑都必须在系统遇到任何输入之前由人类工程师显式写好。

在此定义下，每一次特性新增、缺陷修复、对环境变化的适配，都要求人去（a）理解所需变更、（b）在 D 中定位正确位置、（c）在不引入回归的前提下修改逻辑、（d）验证正确性。每次变更的成本是 D 的规模及其内部依赖密度的函数。

### 2.2 复杂性壁垒

Brooks 区分了“偶然复杂性”（特定实现的产物）与“本质复杂性”（问题固有）。数十年的进步——更高级的语言、框架、自动化测试——系统性地降低了偶然复杂性，但本质复杂性依旧无界。

**命题 2.1（复杂性伸缩）：** 对一个含 n 个组件、每个都可能与任意其他组件交互的系统，可能的交互拓扑数量呈超指数增长；而人类对这些交互进行推理的认知能力本质上是恒定的。

<div class="formula">| 交互拓扑数 | = 2<sup>C(<span class="var">n</span>,&thinsp;2)</sup> = 2<sup><span class="var">n</span>(<span class="var">n</span>&minus;1)/2</sup> = &Theta;(2<sup><span class="var">n</span>²</sup>)</div>

这种错配，正是软件项目随规模增长而边际生产率递减的深层结构性原因。

### 2.3 智能体系统：形式化模型

**定义 2.2（AI 智能体系统）：** AI 智能体系统 A 是一个四元组，其中 M 是作为推理引擎的大语言模型；T 是可执行工具集合（代码解释器、API、数据库、文件系统）；𝓜 是记忆子系统（短期上下文、长期向量库）；Π 是把用户意图分解为动作序列的规划机制。

<div class="formula"><span class="var">A</span> = (<span class="var">M</span>, <span class="var">T</span>, 𝓜, &Pi;)</div>

系统通过迭代执行运作——在时刻 t，模型根据当前状态与记忆选择动作，执行后转移到下一状态：

<div class="formula"><span class="var">a</span><sub>t</sub> = <span class="var">M</span>(<span class="var">s</span><sub>t</sub>, 𝓜) ,&emsp; <span class="var">s</span><sub>t+1</sub> = exec(<span class="var">a</span><sub>t</sub>)</div>

关键区别在于：在智能体系统中，**决策逻辑在运行时生成**。LLM 可动态产生代码、调用工具、并依据中间结果调整行为——这些都未被显式预先编程。它生成的代码不是系统本身，而是按需产生、用后即弃的临时产物。

这与 Karpathy 的“软件 2.0”框架吻合，但更进一步：神经网络不只是替代程序，而是按需**编写**程序，把代码当作服务于更宏大推理目标的工具。这一模式与 ReAct（将推理轨迹与工具使用交织）以及思维链提示（显式的中间推理步骤释放 LLM 潜能）一脉相承。

### 2.4 为何智能体范式的伸缩性不同

设任务 T 的求解需在规模为 N 的空间中推理。在传统范式下：人类工程师必须在脑中遍历该空间以找到解路径；该路径须被编码为静态程序；而人类认知容量 C_H 本质固定；因此当 N > C_H 时，任务在任何现实成本下都不可行。

在智能体范式下：LLM 以随模型规模与训练算力增长的有效容量 C_M 遍历该空间；规划 Π 把 T 分解为可独立处理的子问题；代码只为特定解路径生成，而非为所有可能情形；随着 LLM 能力（指数级）提升，C_M 相应增长。因此，智能体范式将求解能力与人类认知极限**解耦**。这不是 10% 的改进，而是“哪类问题能被经济地解决”的质变。

## 3. 从 SaaS 到 AaaS：第三次范式转变

### 3.1 软件交付的三代演进

商业软件的历史可理解为复杂性持续地从终端用户身上转移的过程：

| 世代 | 核心机制 | 复杂性承担者 | 收入模型 | 代表 |
|------|----------|--------------|----------|------|
| 软件 1.0（本地） | 代码+数据在本地执行 | 终端用户（安装、维护） | 授权销售 | Microsoft、Oracle |
| 软件 2.0（SaaS） | 代码+数据在云端执行 | 厂商（基础设施、更新） | 订阅 | Salesforce、AWS |
| 软件 3.0（AaaS） | 智能体在云端自主运作 | 智能体（理解、构建、运行） | 按结果计费 | OpenAI、Anthropic |

每次转变都遵循同一模式：最有能力吸收复杂性的一方吸收它，最无力管理它的一方被解放。SaaS 把企业从机房中解放；AaaS 则承诺把它们从“必须指定结果如何产生”中解放——它们只需指定想要**什么**结果。

### 3.2 “AI → 软件 → 结果”的失败

迄今主流的企业 AI 范式是“AI 增强开发”：用 LLM 帮助人类工程师在传统软件生命周期内更快写代码。这一“AI → 软件 → 结果”流水线有三个结构性弱点：

1. **瓶颈仍在。** 人类工程师仍是设计决策、架构、集成测试与部署的关键路径；AI 只是加速了实现中的一个子步骤。
2. **复杂性天花板未变。** 最终交付物仍是传统软件系统 S=(C,D,E)，其复杂性仍随 D 的规模伸缩，任何修改仍需人类理解。
3. **迭代延迟。** 即便有 AI 协助，任何功能变更仍要走完“需求→设计→编码→测试→部署”全链路，无法降到人类沟通协调速度之下。

### 3.3 “智能体 → 结果”：智能体即软件

另一种范式坍缩了软件与其执行之间的区分：**智能体即软件**。它在单一集成回路中推理、生成代码、执行并交付结果：（1）人向智能体表述意图与约束；（2）智能体自主规划、执行（按需生成代码）、验证并交付结果；（3）人审计结果并给出反馈。

在此模型中，智能体既是软件又是其操作者。它可能生成上千行代码、执行数据库查询、调用外部 API、产出可视化——全部是临时性的，服务于结果。持久存在的不是中间代码，而是智能体的**能力**。

## 4. 智能体工程：扩展学科

### 4.1 界定该领域

智能体工程由 LangChain 于 2026 年 4 月正式提出，定义为“一种多智能体协调模型：AI 智能体作为数字团队成员——各有明确角色、共享记忆、统一可观测层——驱动软件走完整个交付流水线，而不仅仅是更快地生成代码”。我们主张：智能体工程不取代软件工程，而是扩展它——智能体本身就是软件，构建、部署与治理智能体系统正是该学科的下一前沿。

基于 LLM 的软件工程智能体可归纳为三大核心模块：**感知**（多模态输入处理）、**记忆**（语义、情景、程序性）、**行动**（内部推理 + 外部工具使用），全部由 LLM 推理核心编排。

<figure>
    <img src="{{ site.baseurl }}/img/paper-agent-framework.png" alt="面向软件工程的基于 LLM 的智能体框架">
    <figcaption><b>图 1：</b>面向软件工程的基于 LLM 的智能体框架（改编自 Wang et al.）。感知模块处理多模态输入；记忆模块维护语义、情景与程序性知识；行动模块执行内部推理与外部工具调用；三者由 LLM 推理核心统一编排，并与外部环境交互。</figcaption>
</figure>

Nous Research 的开源框架 Hermes Agent 是这一架构的具体实现，其最重要的特征是闭环学习：完成复杂任务后，智能体自主创建可复用的“技能”（参数化程序模块），并在后续使用中自我改进、在发现不足时自动打补丁。

### 4.2 智能体工程 vs. 传统软件工程

| 维度 | 传统软件工程 | 智能体工程 |
|------|--------------|------------|
| 核心产物 | 源代码（静态） | 智能体系统（动态） |
| 控制中心 | 人类工程师 | LLM 推理引擎 |
| 决策机制 | 预先设计的逻辑 | 运行时生成的推理 |
| 开发周期 | 线性（设计→编码→测试） | 自主迭代回路 |
| 人的角色 | 代码作者 | 意图架构师、协调者、审计者 |
| 复杂性天花板 | 人类认知（O(1)） | 模型容量（随算力增长） |
| 输出单元 | 可运行的软件 | 交付的结果 |
| 错误处理 | 程序员定义 | 模型自适应 |
| 演化 | 手工重构 | 自我修改 |

### 4.3 重新想象人的角色

最具影响的转变也许在于人的角色。传统范式中，人的价值以产出正确、高效代码的能力衡量；智能体范式中，写代码的技能变得商品化。新的人类差异化能力是：**意图表述**（足够清晰且带约束地指定目标）、**架构监督**（在系统层面理解多智能体应如何协调、共享何种记忆、何处需人类判断介入）、**质量校准**（定义“好”的样子并构建可供智能体自我纠错的评估框架）、**伦理治理**（确保智能体行为符合组织价值、法律要求与社会期待）。

我们相信：随着智能体能力成熟，精通智能体编排者的生产力倍增将远超传统“10 倍工程师”的标杆——不是靠打字更快，而是靠协调“智能体集群”奔向复杂结果。天花板不是固定的，它随每一次模型能力与编排基础设施的进步而抬升。

## 5. 实证证据与当前局限

### 5.1 突破性结果

- **SWE-bench Verified。** Lingma SWE-GPT 72B（开放的、以开发过程为中心的模型）解决了 SWE-bench Verified 上 30.20% 的 GitHub issue，接近 GPT-4o 的 31.80% 而完全开源；即便 7B 版本也解决了 18.20%，证明在“过程数据”而非仅静态代码上训练时，小模型也能完成有意义的自动化软件工程——相对 Llama 3.1 405B（近 6 倍大）提升了 22.76%。
- **多智能体协调。** 在 20 多个企业调试工作流中部署协调的智能体集群，将根因定位时间缩短了 93%，单月节省 200 多个工程小时。关键收益来自编排——跨智能体维护共享上下文、并行调查、交叉验证。
- **自演化。** 拥有超过 17.9 万 GitHub stars 的开源框架 Hermes Agent 提供了生产系统中最完整的自演化实现：创建技能 → 使用 → 检测不足 → 自动打补丁，全程无需人类介入。
- **泛化性。** 数百项研究将基于 LLM 的智能体应用于需求分析、架构设计、代码生成、测试、调试、部署、维护的全生命周期，表明该模式可跨软件工程活动泛化。

### 5.2 持续存在的挑战

EvoClaw 基准提供了最发人深省的数据。它要求智能体进行**持续软件演化**——不是孤立的 issue 修复，而是跨提交历史的持续开发，每次变更都须保持系统完整性、错误会累积。其关键发现：

> “整体性能分数从孤立任务上的 >80% 显著下降到持续场景中的至多 38%，暴露了智能体在长期维护与错误传播上的严重困境。”

<figure class="evoclaw-chart">
    <div class="bar-row">
        <span class="bar-label">孤立任务 / Isolated</span>
        <span class="bar-track"><span class="bar-fill good" style="width:82%;">82%</span></span>
    </div>
    <div class="bar-row">
        <span class="bar-label">持续演化 / Continuous</span>
        <span class="bar-track"><span class="bar-fill bad" style="width:38%;">38%</span></span>
    </div>
    <div class="chart-drop">▼ 成功率下降约 54%</div>
    <figcaption><b>图 2：</b>智能体在 EvoClaw 基准上的表现。在评估“持续软件演化”（跨提交持续开发、错误会累积）时，成功率从 80% 以上骤降至至多 38%。数据基于 4 个智能体框架下 12 个前沿模型的评测。</figcaption>
</figure>

由此揭示四大核心挑战：**上下文漂移**（代码库超出有效上下文窗口后，智能体失去对系统级不变量与依赖的连贯理解）、**错误传播**（早期提交的小错误级联放大，智能体缺乏稳健的检测与恢复机制）、**技术债意识**（智能体不为其设计决策的长期成本建模，只优化当下任务完成）、**验证保真度**（自动化测试仍不完整，智能体可能通过测试却引入只在新输入下显现的细微语义错误）。

孤立任务（>80%）与持续演化（<38%）之间的差距，量化了当前智能体能力与“完全自主软件工程”门槛之间的距离。这一差距并非根本性的——它反映的是上下文管理、记忆架构与验证机制上的局限，而这些正是活跃的研究方向。

## 6. 演化路线图

| 阶段 | 智能体能力 | 关键技术 | 人的角色 | 代表系统 |
|------|------------|----------|----------|----------|
| I. 工具增强 | 代码补全、单 issue 修复、简单脚本生成 | 上下文学习、RAG | 作者+评审 | GitHub Copilot、Claude Code |
| II. 单任务自主 | 端到端特性构建、调试、基础维护 | 规划+工具使用、自我纠错 | 意图架构师+审计者 | Devin、OpenHands |
| III. 多智能体团队 | 协调集群处理大型系统、全生命周期管理 | 共享记忆、角色专精、编排 | PM+架构师+审计者 | LangChain 编排、MetaGPT |
| IV. 自演化生态 | 自主发现、学习、复制、适应 | 元学习、自我修改、生态治理 | 目标设定者+伦理治理者 | AGI 助手（前瞻） |

- **阶段 I：工具增强（2023–2025）** 当前主流模式，智能体在人类主导的工作流中充当助手。
- **阶段 II：单任务自主（2025–2027）** 智能体开始从规格到部署地拥有完整任务，人从“做”转向“指定要做什么并验证做得如何”。
- **阶段 III：多智能体团队（2026–2029）** 专精智能体如团队般协调，镜像人类工程组织；共享记忆与可观测性成为关键基础设施。
- **阶段 IV：自演化生态（2028+）** 智能体能改进自身架构、为新领域孵化子智能体、无需人类介入地适应环境变化；“软件”与“智能体”的区分彻底消融，人类介入上移至元层级治理。

## 7. 启示与建议

**对从业者：** （1）从代码生产转向意图工程；（2）建立智能体编排能力；（3）投资可观测性基础设施；（4）采取“人在回路、智能体在驾驶位”的姿态。

**对研究者：** （1）长上下文状态管理；（2）开放式环境中的验证；（3）规模化的智能体对齐；（4）经济模型（按结果计费可能取代订阅与按量计费）。

**对组织：** （1）识别“智能体就绪”的工作流（成功标准清晰、范围明确、已有测试设施者为理想起点）；（2）投资评估框架；（3）重新设计团队结构（更小的“智能体编排者”团队可能取代更大的开发者团队）。

## 8. 结论

AI 智能体的出现，构成了“软件是什么”的范式转变，而不仅仅是“如何构建软件”。从“AI → 软件 → 结果”到“智能体 → 结果”并不消灭软件——智能体就是软件，只是一种根本不同的软件：决策逻辑在运行时生成而非预先编码，系统可在无人类介入下演化。这是软件工程学科的自然延伸，而非其终结。

我们仍处早期。EvoClaw 等基准揭示了孤立任务表现与持续自主开发之间的鲜明差距。当下需要的是雄心勃勃却有所校准的投入：拥抱智能体软件这一新范式，同时认清完全自主的系统仍是一项需要数年的研究挑战。智能体工程代表着软件工程学科的根本扩展——它的从业者不是学会了新工具的程序员，而是一种新型专业人士：引导 AI 智能体奔向复杂结果的意图架构师。旧的软件工程没有终结；它正在成长为更宏大的东西。

</div>

<div class="i18n-en" markdown="1">

> **Source**: Zhenfeng Cao, *Agentic Software: How AI Agents Are Restructuring the Software Paradigm*, arXiv:2606.05608v2 [cs.SE], June 2026. This is a faithful bilingual rendition; use the language button in the navbar to switch between Chinese and the English original.

## Abstract

For over half a century, software engineering has operated on a foundational premise: human engineers decompose problems, encode decision logic into static code, and manually adapt that code as requirements evolve. This paper argues that the emergence of AI agents – systems where large language models serve as the primary reasoning engine, dynamically generating and discarding code as an instrumental resource – constitutes a fundamental restructuring of what software is, not an incremental tool improvement.

We formalize the distinction between traditional deterministic software and agentic software: in the former, code is the carrier of pre-written decision logic; in the latter, **the agent itself is the software**, and its decision logic is generated at runtime. We trace the historical arc from licensed software to SaaS to Agent-as-a-Service (AaaS), showing that each shift transferred additional complexity away from end-users – with the agentic shift transferring not just operational complexity but decision-making complexity itself.

We introduce Agentic Engineering as an expansion of the software engineering discipline into a new paradigm, distinct in its core object of study (agent systems rather than static source code), its control model (LLM-driven rather than human-predefined), and its human role (intent architect rather than code author). Through analysis of recent benchmark evidence including SWE-bench Verified, EvoClaw, and LangChain's multi-agent coordination studies, we demonstrate both the transformative potential of the agentic paradigm and its current limitations, concluding with a four-stage roadmap toward self-evolving agent ecosystems.

## 1. Introduction

Software engineering, as codified at the 1968 NATO Conference, was born from a crisis: systems were growing in complexity beyond what ad-hoc programming practices could manage. The discipline's founding insight was that rigorous methodologies—structured design, modular decomposition, configuration management, systematic testing—could tame this complexity. For five decades, this bet largely paid off. We moved from waterfall to agile, from monoliths to microservices, from manual deployment to CI/CD.

Yet a deeper structural problem persisted. As Brooks observed in *The Mythical Man-Month*, software complexity exhibits a fundamentally different scaling behavior than other engineering domains. Software has no manufacturing step—the design is the product. Every new feature, every edge case, every integration point adds to what Brooks characterized as "essential complexity": complexity inherent to the problem itself, not accidental to the implementation.

This paper contends that the emergence of AI agents does not merely offer a new tool within the existing paradigm. Rather, it fundamentally restructures the premise on which software engineering was founded – not by ending it, but by expanding its definition. An AI agent is itself software. What distinguishes it is not that it escapes the category of software, but that it represents a **new kind of software** – one where decision logic is no longer pre-written but dynamically generated at runtime. When an LLM can understand a task, decompose it into subtasks, dynamically generate code to execute them, and discard that code when no longer needed, the role of code changes from the system itself to an ephemeral instrument of reasoning.

We make three central claims:

1. **First-Principles Necessity.** The agentic paradigm is not a market preference but an inevitable consequence of complexity scaling laws. LLM-based agents can navigate complexity non-linearly by outsourcing reasoning to models whose capacity grows with training compute.
2. **Software Redefined, Not Replaced.** The transition from "AI → Software → Result" to "Agent → Result" does not eliminate software – the agent itself is software, albeit of a fundamentally different kind. It collapses the intermediary: the agent is simultaneously the software system and its execution engine.
3. **Emergent Discipline.** Agentic Engineering is emerging as a distinct practice with its own concepts, tools, and metrics. Its practitioners are a fundamentally different role: intent architects, agent coordinators, and outcome auditors.

## 2. First-Principles Analysis

### 2.1 The Nature of Traditional Software

**Definition 2.1 (Traditional Software System).** A traditional software system S is a tuple where C is a set of computational resources (CPU, memory, I/O); D is a set of deterministic decision rules encoded in source code; E is an execution environment that evaluates D against inputs to produce outputs.

<div class="formula"><span class="var">S</span> = (<span class="var">C</span>, <span class="var">D</span>, <span class="var">E</span>)</div>

The critical property is that **D is static with respect to execution**: all decision logic must be explicitly written by human engineers before the system encounters any input.

Under this definition, every feature addition, bug fix, and adaptation requires a human to (a) understand the change needed, (b) locate the correct position in D, (c) modify the logic without introducing regressions, and (d) verify correctness. The cost of each change is a function of the size of D and the density of its internal dependencies.

### 2.2 The Complexity Barrier

Brooks distinguished between accidental complexity (artifacts of particular implementations) and essential complexity (inherent to the problem). While decades of advances have reduced accidental complexity, essential complexity remains unbounded.

**Proposition 2.1 (Complexity Scaling).** For a system with n components, each potentially interacting with any other, the number of possible interaction topologies grows super-exponentially, while human cognitive capacity to reason about these interactions is essentially constant.

<div class="formula">| interaction topologies | = 2<sup>C(<span class="var">n</span>,&thinsp;2)</sup> = 2<sup><span class="var">n</span>(<span class="var">n</span>&minus;1)/2</sup> = &Theta;(2<sup><span class="var">n</span>²</sup>)</div>

This mismatch is the deep structural reason why software projects experience declining marginal productivity as they grow.

### 2.3 Agentic Systems: A Formal Model

**Definition 2.2 (AI Agent System).** An AI agent system A is a tuple where M is a large language model serving as the reasoning engine; T is a set of executable tools (code interpreters, APIs, databases, file systems); 𝓜 is a memory subsystem (short-term context, long-term vector store); Π is a planning mechanism that decomposes user intent into action sequences.

<div class="formula"><span class="var">A</span> = (<span class="var">M</span>, <span class="var">T</span>, 𝓜, &Pi;)</div>

The system operates iteratively — at step t, the model selects an action from the current state and memory, then transitions to the next state:

<div class="formula"><span class="var">a</span><sub>t</sub> = <span class="var">M</span>(<span class="var">s</span><sub>t</sub>, 𝓜) ,&emsp; <span class="var">s</span><sub>t+1</sub> = exec(<span class="var">a</span><sub>t</sub>)</div>

The key distinction is that in an agentic system, the **decision logic is generated at runtime**. The code it generates is not the system; it is a transient artifact, produced and discarded as needed. This maps to Karpathy's "Software 2.0" but extends it: the neural network does not merely replace the program—it writes programs on demand, consistent with the ReAct framework and Chain-of-Thought prompting.

### 2.4 Why the Agentic Paradigm Scales Differently

For a task T whose solution requires reasoning over a space of size N — under the traditional paradigm, a human must mentally traverse this space, encode the path as a static program, and human capacity C_H is fixed; thus for N > C_H the task is infeasible at any realistic cost. Under the agentic paradigm, the LLM traverses the space with capacity C_M that scales with model size and compute; the plan decomposes T into subproblems; code is generated only for the specific solution path. Thus the agentic paradigm **decouples solution capacity from human cognitive limits** — a qualitative change in what problems can be economically addressed.

## 3. From SaaS to AaaS: The Third Paradigm Shift

### 3.1 Three Generations of Software Delivery

| Generation | Core Mechanism | Complexity Owner | Revenue | Exemplars |
|------------|----------------|------------------|---------|-----------|
| Software 1.0 (Local) | Code + data execute on-premise | End-user (install, maintain) | License sale | Microsoft, Oracle |
| Software 2.0 (SaaS) | Code + data execute in cloud | Vendor (infra, updates) | Subscription | Salesforce, AWS |
| Software 3.0 (AaaS) | Agent autonomously operates in cloud | Agent (understand, build, run) | Outcome-based | OpenAI, Anthropic |

Each transition follows the same pattern: the party best positioned to absorb complexity absorbs it. SaaS liberated businesses from server rooms; AaaS promises to liberate them from specifying *how* a result should be produced—they need only specify *what* result they want.

### 3.2 The Failure of "AI → Software → Result"

The dominant enterprise AI paradigm to date has been AI-augmented development. This pipeline has three structural weaknesses: (1) **Bottleneck persistence** — the human engineer remains the critical path; (2) **Complexity ceiling intact** — the deliverable remains a traditional system S=(C,D,E); (3) **Iteration latency** — any change still traverses requirements → design → code → test → deploy.

### 3.3 "Agent → Result": The Agent as Software

The alternative paradigm collapses the distinction between software and its execution: **the agent is the software**. (1) The human articulates intent and constraints; (2) the agent autonomously plans, executes (generating code as needed), validates, and delivers; (3) the human audits the outcome. The agent is both the software and its operator; what persists is not the intermediate code but the agent's capability.

## 4. Agentic Engineering: Expanding the Discipline

### 4.1 Defining the Field

Agentic Engineering, formally introduced by LangChain in April 2026, is "a multi-agent coordination model where AI agents function as digital team members—each with defined roles, shared memory, and a unified observability layer—to drive software through the entire delivery pipeline." It does not replace software engineering but expands it. LLM-based agents comprise three core modules: **Perception** (multi-modal input), **Memory** (semantic, episodic, procedural), and **Action** (internal reasoning + external tool use), orchestrated by the LLM reasoning core.

<figure>
    <img src="{{ site.baseurl }}/img/paper-agent-framework.png" alt="LLM-based agent framework for software engineering">
    <figcaption><b>Figure 1:</b> The LLM-based agent framework for software engineering (adapted from Wang et al.). The perception module handles multi-modal input; the memory module maintains semantic, episodic, and procedural knowledge; the action module executes both internal reasoning and external tool invocations — all orchestrated by the LLM reasoning core, interacting with the external environment.</figcaption>
</figure>

Hermes Agent (Nous Research) realizes this with a closed learning loop: it autonomously creates reusable Skills that self-improve and self-patch.

### 4.2 Contrasting Agentic and Traditional Engineering

| Dimension | Traditional SE | Agentic Engineering |
|-----------|----------------|---------------------|
| Core artifact | Source code (static) | Agent system (dynamic) |
| Control center | Human engineer | LLM reasoning engine |
| Decision mechanism | Pre-designed logic | Runtime-generated reasoning |
| Development cycle | Linear (design→code→test) | Autonomous iterative loop |
| Human role | Code author | Intent architect, coordinator, auditor |
| Complexity ceiling | Human cognition (O(1)) | Model capacity (growing with compute) |
| Output unit | Functioning software | Delivered outcomes |
| Error handling | Programmer-defined | Model-adaptive |
| Evolution | Manual refactoring | Self-modification |

### 4.3 The Human Role Reimagined

In the agentic paradigm, code-generation skill becomes commoditized. The new human differentiators are: **Intent articulation**, **Architectural oversight**, **Quality calibration** (defining what "good" looks like and building evaluation frameworks for self-correction), and **Ethical governance**. The productivity multiplier for those who master agent orchestration will far exceed the traditional "10x engineer" benchmark—not through faster typing, but through coordinating swarms of agents toward complex outcomes.

## 5. Empirical Evidence and Current Limitations

### 5.1 Breakthrough Results

- **SWE-bench Verified.** Lingma SWE-GPT 72B resolves 30.20% of GitHub issues—approaching GPT-4o's 31.80% while fully open. Even the 7B variant resolved 18.20%, a 22.76% relative improvement over the ~6× larger Llama 3.1 405B.
- **Multi-Agent Coordination.** Coordinated agent swarms across 20+ enterprise debugging workflows reduced root-cause identification time by 93%, saving over 200 engineering hours in a single month—gains from orchestration, not better individual agents.
- **Self-Evolution.** Hermes Agent (179,000+ GitHub stars) implements a closed loop: create skill → use → detect weakness → self-patch, without human intervention.
- **Generalization.** Hundreds of studies apply LLM-based agents across the full lifecycle, suggesting the pattern generalizes across software engineering activities.

### 5.2 Persistent Challenges

The EvoClaw benchmark requires **continuous software evolution**—sustained development across commit histories where errors accumulate. Its key finding:

> "Overall performance scores drop significantly from > 80% on isolated tasks to at most 38% in continuous settings, exposing agents' profound struggle with long-term maintenance and error propagation."

<figure class="evoclaw-chart">
    <div class="bar-row">
        <span class="bar-label">Isolated Tasks</span>
        <span class="bar-track"><span class="bar-fill good" style="width:82%;">82%</span></span>
    </div>
    <div class="bar-row">
        <span class="bar-label">Continuous Evolution</span>
        <span class="bar-track"><span class="bar-fill bad" style="width:38%;">38%</span></span>
    </div>
    <div class="chart-drop">▼ ~54% drop in success rate</div>
    <figcaption><b>Figure 2:</b> Agent performance on the EvoClaw benchmark. When evaluated on continuous software evolution (sustained development across commits with error accumulation), success rates collapse from over 80% to at most 38%. Data based on 12 frontier models across 4 agent frameworks.</figcaption>
</figure>

Four core challenges follow: **context drift**, **error propagation**, **technical-debt awareness**, and **verification fidelity** (agents can pass tests while introducing subtle semantic errors). The gap between isolated-task (>80%) and continuous-evolution (<38%) quantifies the distance to fully autonomous software engineering. This gap is not fundamental—it reflects active research areas in context management, memory architecture, and verification.

## 6. Evolutionary Roadmap

| Stage | Agent Capability | Key Technologies | Human Role | Representative Systems |
|-------|------------------|------------------|------------|------------------------|
| I. Tool-Augmented | Code completion, single-issue fixes | In-context learning, RAG | Author + reviewer | GitHub Copilot, Claude Code |
| II. Single-Task Autonomous | End-to-end feature building, debugging | Planning + tool use, self-correction | Intent architect + auditor | Devin, OpenHands |
| III. Multi-Agent Teams | Coordinated swarms, full-lifecycle mgmt | Shared memory, role specialization, orchestration | PM + architect + auditor | LangChain orchestration, MetaGPT |
| IV. Self-Evolving Ecosystems | Autonomous discovery, learning, adaptation | Meta-learning, self-modification, governance | Goal setter + ethics governor | AGI assistants (prospective) |

- **Stage I (2023–2025):** Agents as assistants within human-led workflows.
- **Stage II (2025–2027):** Agents own complete tasks from spec to deployment; humans shift to specifying and verifying.
- **Stage III (2026–2029):** Specialized agents coordinate as teams; shared memory and observability become critical infrastructure.
- **Stage IV (2028+):** The distinction between "software" and "agent" dissolves; human involvement shifts to meta-level governance.

## 7. Implications and Recommendations

**For practitioners:** shift from code production to intent engineering; build agent-orchestration competence; invest in observability; adopt a "human-in-the-loop, agent-in-the-driver's-seat" posture.

**For researchers:** long-context state management; verification in open-ended settings; agent alignment at scale; new economic models (outcome-based pricing).

**For organizations:** identify agent-ready workflows; invest in evaluation frameworks; redesign team structures (smaller teams of "agent orchestrators").

## 8. Conclusion

The emergence of AI agents constitutes a paradigm shift in what software *is*, not merely how it is built. The agent is software, but of a fundamentally different kind: decision logic generated at runtime rather than pre-encoded, and a system that can evolve without human intervention. This is a natural progression of the software engineering discipline, not its termination.

Yet we are still in the early stages. Benchmarks like EvoClaw reveal a stark gap between isolated-task performance and sustained autonomous development. The moment calls for ambitious but calibrated investment. Agentic Engineering represents a fundamental expansion of the discipline: its practitioners are not programmers who learned new tools but a new kind of professional—intent architects who direct AI agents toward complex outcomes. The old software engineering is not ending; it is growing into something larger.

</div>
