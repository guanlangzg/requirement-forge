# 设计说明：吸收了哪些机制，放弃了哪些

本 skill 的每一条规则都能追到参考库里的某个文件。这份文档把对应关系列清楚，并说明为什么有些机制没有照搬——有些是因为与用户的要求冲突，有些是因为它们解决的是另一个问题。

## 溯源基准

下文所有引用的路径，都是相对下列上游仓库根目录的路径。参考的是 2026-10-21 的默认分支版本。

| 简称 | 仓库 | 许可证 |
| :--- | :--- | :--- |
| mattpocock-skills | [mattpocock/skills](https://github.com/mattpocock/skills) | MIT |
| addyosmani-agent-skills | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | MIT |
| spec-kit | [github/spec-kit](https://github.com/github/spec-kit) | MIT |
| khazix-skills | [KKKKhazix/khazix-skills](https://github.com/KKKKhazix/khazix-skills) | MIT |
| anthropics-skills | [anthropics/skills](https://github.com/anthropics/skills) | 见仓库 |
| chenlu-hung | [chenlu-hung/my-skills](https://github.com/chenlu-hung/my-skills) | 未声明 |
| max4c | [max4c/skills](https://github.com/max4c/skills) | MIT |
| EricTechPro | [EricTechPro/grill-me-checkpoint](https://github.com/EricTechPro/grill-me-checkpoint) | MIT |
| KAIXUAN | [KAIXUAN-zhang-bot/grillme-skill](https://github.com/KAIXUAN-zhang-bot/grillme-skill) | MIT |

本 skill 未复制任何上游代码，只借鉴了设计思路与少量表述性短语，全部为重新撰写。

---

## 一、直接吸收的机制

### 1. 决策树 + 前沿

**来源**：`mattpocock-skills/skills/productivity/grilling/SKILL.md:6-26`

原文的机制是：需求是一棵决策树，每个决定分叉出依赖它的下游决定；**前沿**是"前置已定、现在能问而不必猜"的那批问题；依赖本轮未决问题的，属于下一轮。

**本 skill 的用法**（`SKILL.md` 阶段三「一次一问，但不乱序」）：保留了决策树与前沿的概念和"依赖未决就不能问"的纪律，但**改变了提问颗粒度**——原文一轮问完整条前沿，本 skill 一次只问一个。前端按影响大小从前沿里挑。

这一处改动来自用户明确要求「必须要提问你不清楚的，但是得一个个来」。如果只做"一次一问"而丢掉前沿约束，问题顺序会乱：问到被上游决定支配的下游问题，答了也白答。「导出给谁用 / 要不要脱敏」这个例子就是原文依赖关系的直接应用。

### 2. 事实归 agent，决定归用户

**来源**：`grilling/SKILL.md:28`；中文版见 `KAIXUAN/SKILL.md:27`

> 原文："Finding *facts* is your job, never the user's... The *decisions* are the user's: put each to them and wait."

**本 skill 的用法**：守则第一条原文照搬，并在阶段二落实为「先去查实际情况」，在阶段三落实为三道闸的第二条「我能自己查到吗」。

### 3. 提问前过三道闸

**来源**：`chenlu-hung/grill-me/SKILL.md:10-13`

原文的三条检验：答案会改变下游吗、代码库能答吗、有明显默认值吗。

**本 skill 的用法**：阶段三「提问前过三道闸」，三条几乎逐字保留，只把"代码库"扩展成"代码、文件、环境、公开文档"，因为本 skill 也要处理非软件任务。

原文还提供了「Relentless means thorough on what matters, not exhaustive on everything」这句定调——这句是"逐个来"不会退化成"问个不停"的关键，故保留为「什么时候可以不问了」的存在前提。

### 4. 每题附推荐答案

**来源**：`grilling/SKILL.md:8`（"give your recommended answer"）、`grilling/SKILL.md:24`（"Word each question so 'yes' accepts your recommended answer"）

**本 skill 的用法**：阶段三每题格式里的「推荐：X（理由：Y）」，以及把「随便」变成明确选择那段：

> 我倾向 A，因为 <理由>。选 B 的代价是 <代价>。用 A 吗？

### 5. 附猜测比让用户从零生成更快

**来源**：`addyosmani-agent-skills/skills/interview-me/SKILL.md:71-77`

原文：用户对错误猜测的反应快于从空白生成答案；附猜测还能逼自己显式暴露假设。原文同时警告风险是"礼貌性附和"，对策是"偶尔往你预期会被反驳的方向猜"。

**本 skill 的用法**：推荐答案不只是"抄个答案"，要带理由；阶段一先给「我推断」而不是空问"你到底想要什么"，也是同一条原理——给用户一个可反驳的靶子。

### 6. 置信度数字

**来源**：`interview-me/SKILL.md:40-51`

原文要求先写一句假设 + 0–100% 置信度，低于 ~70% 必须在同一行补一句"还缺什么"。

**本 skill 的用法**：阶段一「我推断」后面附 `（置信度 ~50%：缺数据量、更新频率、使用人数）`。保留"低于 70% 要说明缺什么"，因为这句话直接告诉用户下一轮该帮我们补什么。

### 7. 识破"应该说"的答案

**来源**：`interview-me/SKILL.md:79-92`

原文列了四种套话信号（最佳实践式、从众式、"我应该……"式、把 buzzword 当目标），并给出那一记反问：

> *"If you didn't have to justify this to anyone, what would you actually want?"*

**本 skill 的用法**：阶段三「识破'应该说'的答案」整段吸收，反问译作「如果不用向任何人交代，你实际想要的是什么？」。另在 `references/question-bank.md` 末尾整理成 9 条「反问题」对照表。

### 8. "随便"不是回答

**来源**：`interview-me/SKILL.md:113-122`

原文列了四种不是"yes"的回应：随便你 / 听起来不错 / 那就开始吧 / 沉默后开始。

**本 skill 的用法**：阶段三「'随便'不是回答」。原文的处理（重新给两个具体选项）保留。原文 Step 5 的"逐个确认显式 yes"，落在守则「共识前不执行」的"用户说'就这样吧'要确认一次"。

### 9. 停止条件：五维模糊度评分

**来源**：`max4c/skills/grill-me/SKILL.md:61-165`

原文用 Goals / Acceptance / Boundaries / Alternatives / Assumptions 五个维度，每维打 0 / 0.25 / 0.5 / 0.75 / 1，取平均，按模式设阈值（spec 0.2 / ticket 0.3 / freeform 0.4）。原文还强调：**评的是产物，不是访谈**——用户答了但答案仍模糊，那一维就还是高分。

**本 skill 的用法**：阶段三「什么时候可以不问了」整张表来自这里，五个维度（目标 / 验收 / 边界 / 备选 / 假设）原样保留。改动只有一处：三档阈值合成单档 **0.35**。理由是本 skill 不区分 spec / ticket / freeform 三种模式，取中间偏严的值——需求文档通常要拿来开工，不能像 exploratory grilling 那样放宽。

保留了原文的「别数问题、别做算术」纪律和「拿不准就往模糊方向打分」。

### 10. 兜底：预测不出就说明卡在根子上

**来源**：`interview-me/SKILL.md:132-140`（95% 置信停止 + "问了几轮还预测不出，说明缺的是根子上的东西"）

**本 skill 的用法**：阶段三末尾「兜底」段。这是防止"逐个来"变成无限追问的唯一保险。

### 11. 边问边落盘，不攒到最后

**来源**：`EricTechPro/skills/grill-me-checkpoint/SKILL.md:34-44`（Checkpoint Rule：每答完一题先写文件再问下一题）；`mattpocock-skills/skills/engineering/domain-modeling/SKILL.md:62`（"Don't batch these up: capture them as they happen"）

**本 skill 的用法**：阶段四「不要攒到最后才写」。理由照原文的意思重新表述：上下文会丢，文件才是唯一可靠记录。

这里做了一处合并：EricTechPro 是**每个答案写一次**，本 skill 是**每定一件事写一次**——因为本 skill 一次只问一题，两者实际等价，但不必单独维护一个中间 capture 文件再二次加工。

### 12. 矛盾当场摆出来，不选一边走

**来源**：`domain-modeling/SKILL.md:44-58`（Challenge against the glossary / Sharpen fuzzy language / Cross-reference with code）

原文的形态是把矛盾**当场**提出来问（"你 glossary 里 cancel 是 X，你刚才说的是 Y，到底是哪个？"），而不是记下来后面处理。

**本 skill 的用法**：阶段一的「矛盾 / 含糊」栏 + 三点要求里的"矛盾要摆出来，不要选一个继续走"。阶段二「不许用推测冒充事实」也是同一条纪律的延伸。

### 13. 剥掉手段，留诉求

**来源**：`khazix-skills/leader/SKILL.md:36`

> 原文："手段当假设检验（说「加 Redis」要的是「变快」，与事实不符就目标进正文、手段写进「我替你拍的板」）"

**本 skill 的用法**：阶段一「剥掉手段」直接用这个例子（Redis → 变快），并提出规则"手段可以换，诉求不能猜"。

### 14. 提案不是替用户拍板

**来源**：`khazix-skills/leader/SKILL.md:14`

> 原文："沉默替领导拍板是越权，摆到明面是尽职"

**本 skill 的用法**：阶段二标题「我的建议（推断，你可以否掉）」+ 规矩第一条。这是"先给建议再提问"能成立的前提——建议必须显式标为可否定，否则就变成了替用户决定。

### 15. 非目标是必填项

**来源**：`addyosmani-agent-skills/skills/idea-refine/SKILL.md:143`（"The 'Not Doing' list is arguably the most valuable part"）；`interview-me/SKILL.md:111`（"Including 'Out of scope' is non-negotiable. Half of misalignment is silent disagreement about what is *not* being built."）

**本 skill 的用法**：模板 §2 的「非目标（明确不做）」+ 成文硬要求第 2 条。原句"一半的错位来自对'不做什么'的沉默分歧"几乎原样保留。

### 16. 建议砍什么比建议加什么值钱

**来源**：综合 `idea-refine` 的 Not Doing 立场与 `leader` 的"压不进就是活太大——拆成独立几件"。

**本 skill 的用法**：阶段二规矩第二条。这是范围失控最常见的地方。

### 17. 问题必须是完整疑问句，且要说清"为什么重要"

**来源**：`spec-kit/templates/commands/clarify.md:143-147`

原文规则：必须以 `**Question:**` 开头、以 `?` 结尾；**绝不能拿主题标签或需求编号当问题**；问句后必须紧跟一句大白话的 "Why it matters"；自检标准是"不懂 Spec Kit 的人只看问句也能回答"。

**本 skill 的用法**：阶段三每题格式里的 `> 为什么重要：……`。原文那条"标签不是问题"的禁令也保留——本 skill 的题目一律是问句，`Q3｜导出的数据给谁看？` 里编号只是序号，不是题目本身。

### 18. 选项表 + 推荐置顶 + 允许自由作答

**来源**：`spec-kit/templates/commands/clarify.md:148-169`

**本 skill 的用法**：阶段三的选项表（A/B/C + 推荐）。原文的 5 选项上限没有保留（本 skill 用 2–3 个），因为需求盘问里选项过多通常意味着问题没问对——该先问更上游的问题。

保留了原文的「可以只回选项字母」的轻量作答契约。

### 19. 别乱问技术栈

**来源**：`spec-kit/templates/commands/clarify.md:234`（"Avoid speculative tech stack questions unless the absence blocks functional clarity"）

**本 skill 的用法**：并入三道闸第一条的判据（"所有可能的答案都导致同一份产出"）。选型类的问题另放在 `question-bank.md` 第六类，且必须先问"结论用来做什么决定"。

### 20. 答不上来记成待定，标明谁来决定

**来源**：`EricTechPro/skills/grill-me-checkpoint/SKILL.md:40`（"Add flags for anything the user cannot answer, including the likely owner"）；`max4c/skills/grill-me/SKILL.md:164`（「别惩罚未知的东西」：真正还没法知道的属于 Open Question，不算评分失败）

**本 skill 的用法**：模板 §8 的待定表（事项 / 卡在哪 / 谁来决定 / 最晚何时）+ 阶段三「答不上来就记下」。原文"别惩罚未知"的意思也保留了——待定项**不计入**模糊度扣分，只要它被显式记录。

### 21. 决策记录只记难逆转的

**来源**：`domain-modeling/SKILL.md:66-74`（ADR 三条门槛：难逆转 / 无背景会令人惊讶 / 真实权衡的结果，缺一即跳过）

**本 skill 的用法**：模板 §9 + 成文硬要求第 3 条。三条门槛压缩成两句（"只记难逆转、且当时真有替代方案的决定"），但保留了"记理由不只记结论"的核心——原文那句"半年后有人想改回来会先看这一栏"直接落在模板里。

### 22. 禁止判不了的形容词

**来源**：`spec-kit/templates/commands/analyze.md:126`（Ambiguity Detection：flag vague adjectives lacking measurable criteria）

**本 skill 的用法**：成文硬要求第 1 条，把 fast / scalable / secure / intuitive / robust 换成本地语境的「友好 / 高效 / 完善 / 合理的」，并给了更强的判据：**写不出可检验的句子，说明它还没定，回第三阶段问**。

### 23. 文档要能让没参与对话的人照着做

**来源**：`khazix-skills/leader/SKILL.md:8`（"执行者一字不差地执行、把书当唯一真理、中途没人可问……全文不许有『来找我』"）；`max4c/skills/grill-me/SKILL.md:12`（"a spec, ticket, or plan that an agent with zero context could execute without asking the user anything"）

**本 skill 的用法**：模板的验收方式栏 + 成文硬要求。没有直接照抄"零上下文执行"这句话，因为需求文档的读者还有人类；但验收方式要求"陌生人做完后你能判断是否达标"，是同一个标准。

---

## 二、没有吸收的机制及原因

这几项都是参考库里的好设计，但解决的不是同一个问题，或与用户的要求冲突。

### wayfinder（决策票据地图）

`mattpocock-skills/skills/engineering/wayfinder/SKILL.md`，128 行。

它解决的是"一个会话装不下的巨大 effort，路还看不清"。本 skill 的定位是把单个需求锻造清楚。两者可以衔接（需求文档 → wayfinder 的 Destination），但硬塞进来会让 SKILL.md 变成两套流程的拼盘。

如果将来需要：[KAIXUAN-zhang-bot/grillme-skill](https://github.com/KAIXUAN-zhang-bot/grillme-skill) 里有完整的中文版，可直接用。

### doubt-driven-development（对抗评审）

`addyosmani-agent-skills/skills/doubt-driven-development/SKILL.md`，243 行。

它评审的是**已经写好的产物**，用的是"偏见为证伪"的新鲜上下文评审者。本 skill 的产出是需求文档，理论上可以接这一步。但它需要独立上下文，且原文自己就说明了限制（不能在子 agent 里嵌套）。强行内联会让流程臃肿，所以留给下游单独使用。

### discernment-nudge（交付后反问）

`anthropics-skills/skills/discernment-nudge/SKILL.md`。

它问的是"你凭什么信这个答案"，方向与本 skill 相反（本 skill 问"你要什么"）。是本 skill 完成**之后**可叠加的一层，不是一个阶段。

### 一轮扫完整条前沿（广度优先盘问）

`grilling/SKILL.md:8`。与用户「一个个来」的要求直接冲突，已按上文第 1 条改造为"从前沿里挑一个"。

保留的是前沿纪律，放弃的是一次问一整轮。

### to-questionnaire（把问题变成问卷给别人填）

`mattpocock-skills/skills/productivity/to-questionnaire/SKILL.md`。

它的场景是"这件事的答案在别人脑子里"。本 skill 默认答案在用户脑子里。如果第三阶段出现大量"用户答不上来、得去问别人"的待定项，那是 `to-questionnaire` 的活——已在模板 §8 的「谁来决定」栏留了口子，但不内置它的问卷生成流程。

### spec-kit 的扩展 hook 机制

`clarify.md:23-54`。那是给 spec-kit 项目生态用的执行钩子，本 skill 是独立文件，没有对应运行时。

### 三档模糊度阈值

`max4c` 的 0.2 / 0.3 / 0.4。改为单档 0.35，理由见上文第 9 条。

### `disable-model-invocation: true`

mattpocock 的 grill-me / grill-with-docs / wayfinder 都设了它（只能被显式调用）。本 skill **故意不设**：用户的需求描述混乱时，往往是模型自己该主动接管的时刻——等用户先知道有这么一个 skill 再去调它，就太晚了。这与 `diagnosing-skills` 里"description 要写清何时使用"的触发机制配合，靠 description 里的场景词来命中。

---

## 三、原创的合成：为什么"先给读法和建议，再提问"

这是本 skill 与参考库所有文件最大的不同，值得单独说明。

参考库里的提问型 skill 分两类：

- **立刻开问**：grilling（一轮或一题）、interview-me（一题）、max4c、RobMitt。适合用户已经有一个成形方案要压测。
- **先起草再问**：spec-kit clarify（先读现有 spec 再问 ≤5 个）、idea-refine（先 restate 成 How Might We 再问 3–5 个）、leader（先实测代码库再问 ≤5 个）。

本 skill 面对的是"描述很混乱"的输入，比这两类都更靠前。如果立刻开问，用户会感到被审讯，而且问题命中率低——因为他还不知道自己说的话被理解成了什么。如果先起草一份完整 spec 再问，又太重，且那份 spec 建立在一堆猜测上。

所以切成三段：**整理（给读法）→ 推断（给建议）→ 盘问（一题一题钉）**。

前两段让用户用"更正"代替"陈述"——按 interview-me 的论证，用户反驳比从零生成快得多。等他把你的读法改对了，第三个阶段的每个问题才问得准。

这条设计也解释了为什么第一阶段允许**不完美**：读法可以错，错得越具体越好，因为具体的错误比抽象的提问更容易被纠正。

---

## 四、与参考库的口径差异汇总

| 项 | 参考库 | 本 skill | 原因 |
| :--- | :--- | :--- | :--- |
| 提问节奏 | grilling：一轮整条前沿 | 一次一题，从前沿挑 | 用户明确要求 |
| 提问时机 | 大部分立刻开问 | 先整理+推断，再开问 | 输入更混乱，需先对齐理解 |
| 模糊度阈值 | 三档 0.2/0.3/0.4 | 单档 0.35 | 无模式区分，取中间偏严 |
| 写入节奏 | EricTechPro：每答必写 | 每定一件事写一次 | 与一题一次等价，少一层中间文件 |
| 触发方式 | grill 系设 disable-model-invocation | 不设，靠 description 触发 | 需求混乱时需要主动接管 |
| 文档产出 | grill 系不产出文档 | 必须产出文档 | 用户明确要求 |
| 决策记录 | ADR 三条门槛 | 保留三条，压缩表述 | 需求文档里 ADR 太重 |

---

## 五、实测记录

见 `tests/README.md`：5 个真实用例，两轮测试。首轮暴露 6 个问题并修订，回归测试验证修复项与第二轮衔接。
