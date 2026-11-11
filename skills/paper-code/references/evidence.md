# 研究依据与适用边界

证据核验日期：2026-11-12。采用公开一手材料的有界检索，非系统综述、创新性查新或全期刊规范检查。本 Skill 的具体流程是工程设计，不是某篇论文已验证的完整产品。

仅在需要解释文献、术语或适用性依据时查阅；普通整理不加载本文件、不重新联网调研。最小编辑阶梯、按风险分流、按需读取与停止条件也是工程选择，不能由以下文献推出提速比例或实际人类理解提升。指定期刊的当前规则仍需在实际任务中核实。

## 证据到规则

| 来源 | 已核实的内容 | 在 Skill 中的用途 | 不能推出 |
|---|---|---|---|
| [1] Natural Language Outlines | 用简洁自然语言分段概述代码，并讨论代码/自然语言同步；含专业开发者对提纲质量的评价 | 写出计算链并绑定实现位置，核对文档与代码 | 提纲质量评价不等于直接测得人类理解能力或审查速度提升 |
| [2] Verifiable Literate Programming | 在提示与代码之间设置可读文档层；将用户确认的文档性质与模型检查结合 | 强调科学意图、代码行为和验证证据之间的对应 | 普通 Markdown 文档不等于 VLP；本 Skill 不提供该论文的形式化验证器 |
| [3] 真实仓库比较研究 | 摘要报告部分真实仓库代码层面的 AI/人类差异小于实验室研究的印象 | 不预设 AI 代码必然差；按实际实现决定改动 | 代码指标差异小不等于人类同样易懂；观察研究不等于因果结论 |
| [4] AI 可读性修改研究 | 针对 Python 提交的静态指标分析发现，以可读性为目标的 Agent 修改并不保证指标改善 | 不把“让 AI 再重写一遍”当验收；不追逐单一静态评分 | 静态指标变差不等于真实读者理解一定变差；不能外推所有语言 |
| [5] 科学计算最佳实践 | 面向人写程序、用现成测试工具；“Document interfaces and reasons, not implementations”；“Refactor code in preference to explaining how it works” | 最小结构整理、解释原因、回归检查 | 规范性建议不是 AI 效果实验，也不保证所有科研项目需相同架构 |
| [6] JOSS 官方审稿标准 | 重视文档、安装、使用、功能与可客观核查的测试；强烈鼓励自动化测试，但接受有文档的人工步骤 | 检查交付的可运行、可理解和可核查性，允许分层验证 | 不可说全部期刊强制自动测试；本 Skill 不保证满足 JOSS 当前全部投稿条件 |
| [7] Fowler 重构定义 | 改变内部结构而不改变可观察行为 | 把方法变化与表达整理分开，保持外部契约 | 通过有限测试不等于证明所有输入上的可观察行为完全相同 |

## 主张强度

- **高：规范性共识。** 可理解的科研代码和可核查的运行/测试说明是值得保留的交付目标。[5][6] 这不等于有实验测定其效益大小。
- **高：概念与约束。** 行为保持应是“重构”的边界；科学定义不能混在命名或结构整理中悄然变化。[5][7]
- **中：机制合理且有相近研究。** 自然语言中间说明可帮助组织人类检查，但本 Skill 的具体走读流程并未开展人类受试试验。[1][2]
- **不支持：** AI 代码普遍不可读、润色必然提高理解、文档齐全即证明等价、测试通过即证明科学正确、已实现所有期刊的投稿合规。

## 一手来源与阅读深度

[1] **Natural Language Outlines for Code: Literate Programming in the LLM Era** — https://arxiv.org/abs/2408.04820 。2024-08-09 首次提交，2025-04-17 修订。本轮核对摘要；不从摘要推断具体人类理解效应量。

[2] **Guiding Human Validation of LLM-Generated Code via Verifiable Literate Programming** — https://arxiv.org/abs/2607.02333 ；正文 https://arxiv.org/html/2607.02333v1 。2026-07-02 预印本。本轮摘要及 HTML 正文定向摘录；模型检查仅覆盖所建模的性质与假设，不能据此保证任意科研代码等价。

[3] **A Large-Scale Comprehensive Measurement of AI-Generated Code in Real-World Repositories** — https://arxiv.org/abs/2603.27130 。2026-03-28 首次提交，2026-07-01 修订。前一轮直接读取原始摘要支持“小差异”的限定陈述；本轮工具摘要不完整，未进一步核对指标、样本和效应量。不用它给出人类阅读能力结论。

[4] **Do AI Agents Really Improve Code Readability?** — https://arxiv.org/html/2603.13723v1 。2026-03-14 预印本。本轮正文定向摘录；Python、有限提交样本、静态代理指标及混杂提交限制外推。此处不依赖具体百分比制定规则。

[5] **Best Practices for Scientific Computing** — https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1001745 。2014-01-07，PLOS Biology，规范性文章。本轮定向读取可读性、测试、重构与文档建议。

[6] **JOSS Review Criteria** — https://joss.readthedocs.io/en/latest/review_criteria.html ，Tests 定位 https://joss.readthedocs.io/en/latest/review_criteria.html#tests 。官方动态文档，本轮定向核对，访问日期 2026-11-12。自动化测试为强烈鼓励，不是唯一允许的测试形式。期刊规则可能更新，实际投稿必须再查指定期刊当前规则。

[7] **Refactoring** — https://refactoring.com/ 。Martin Fowler 的重构定义，专业一手定义资料，非实验研究。访问日期 2026-11-12，定向读取 observable behavior 定义。

## 未覆盖与失败

- 未检索到能直接证明“投稿前对 AI 科研代码做一次语言编辑”的统一学科规范；这不是不存在的证明。
- FAIR4RS Nature 页面返回 303；fair-software.eu/recommendations 返回 404；未将二者作为本轮结论依据。JOSS 原始 HTML 直取返回 403，WebFetch 定向 Tests 摘录可读；不得把一次访问失败写成规则不存在。
- 中文查询仅定位到培训/技能文章，未获得与本任务同样直接的一手实验依据。
- ScanSci Find MCP 在本轮未暴露，学术路线降级为公开一手网页；未做数据库全量检索、引文闭环或人类理解效果的系统综述。
- 本 Skill 初始验收使用合成任务；真实科研效果仍取决于项目、数据、作者确认与独立读者检查。
