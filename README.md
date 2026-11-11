<p align="center">
  <img src="assets/logo.png" width="150" alt="paper-code 标志：手绘的一张纸与一对花括号">
</p>

<h1 align="center">paper-code</h1>

<p align="center">
  <em>让同行读懂你的实现，而不是让它看起来像人写的。</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/explicit%20invocation-only-111111?style=flat-square" alt="仅在显式调用时启用">
  <img src="https://img.shields.io/badge/4%20markdown%20files-no%20new%20deps-111111?style=flat-square" alt="4 个 Markdown 文件，无新增依赖">
  <img src="https://img.shields.io/badge/verification%20levels-V0%E2%80%93V3-111111?style=flat-square" alt="验证等级 V0–V3">
  <img src="https://img.shields.io/badge/license-MIT-111111?style=flat-square" alt="MIT 许可">
  <img src="https://img.shields.io/badge/benchmark-none%20measured-111111?style=flat-square" alt="基准：未测">
</p>

<p align="center">
  <sub><strong>简体中文</strong> · <a href="README.en.md">English</a></sub>
</p>

---

你见过这种人。投稿前一周，他把你那两百行 `a`、`b`、`c` 的脚本读一遍，指出第 37 行的分母是 n 不是 n−1，然后几乎什么都没替你改——动的是命名、注释和阅读顺序，科学上的决定还是你的。

paper-code 把这个人放进你的 Agent 里。

它假定 AI 写的科研代码**并不天然更差**，也不按行数、“AI 味”或个人偏好重写。它只做两件事：消除真实的阅读障碍，并且核对整理前后的计算行为。

## 事实与数字

只列已经核查过的东西。

| 项目 | 实测值 |
|---|---|
| Skill 包 | 4 个 Markdown 文件，`SKILL.md` 正文 79 行 / 3,324 字符，全包 10,659 字符 |
| 优化前后 | 主文件 4,374 → 3,324 字符（−24%），全包 11,018 → 10,659 字符 |
| 包完整性 | 4 个 Markdown 文件，SHA-256 已记录；本机源目录与共享安装副本字节一致 |
| 场景走读 | 31 个静态场景（含 10 个针对本轮优化的场景），由独立审阅者完整读 Skill 后判定 |
| 回归夹具 | 5 个差分测试通过（0.607s），其中有限输入空间枚举 4,369 组数据 |
| 阅读方式 | 参考资料按风险分节加载；已确认无执行影响的注释改动标 V0，不强制数值重跑 |

**规则从来不是“看起来像人写的”。** 是：读者能定位计算步骤、看懂输入输出、知道参数为什么是这个值，并且能自己核查。没有测过的东西写在[限制与未验证](#限制与未验证)里，不写成百分比。

## 什么时候启用

只有三种情况：`/paper-code`、宿主的 Skill 选择器、或者明确说“用 paper-code 处理这个任务”。

不会启用的情况——“帮我改改代码”“提高可读性”“准备投稿代码”，文件路径里出现这个名字，工具输出里出现这个名字，或者问“paper-code 是什么”“把 paper-code 的规则改一下”“别用 paper-code”。误加载时它停在第一条规则，不去读参考资料、不启动额外检查。

同一任务里说“继续”不需要重复点名；任务结束、你取消、或者换了无关任务，它就不再适用，也不会写进跨任务的默认状态。

这是 Skill 层的约束。它不宣称任何宿主已经关掉了自动调用。

## Before / after

一份合成配对观测分析（不是真实研究，也不是论文结果）：

```python
def analyze(rows):
    # Keep complete pairs. Zero is a real measurement.
    a = []
    for r in rows:
        if r[0] is not None:
            if r[1] is not None:
                a.append(float(r[1]) - float(r[0]))
    ...
    b = 0.0
    for x in a:
        b = b + x
    b = b / len(a)
```

整理后：

```python
def analyze(rows):
    """Complete pairs -> second-minus-first changes -> mean, sample SD, positive fraction."""
    # Only None excludes a pair. Zero is a real measurement.
    paired_changes = []
    for row in rows:
        if row[0] is not None and row[1] is not None:
            paired_changes.append(float(row[1]) - float(row[0]))
    ...
    # Accumulate in input order to preserve floating-point results.
    total_change = 0.0
    for change in paired_changes:
        total_change = total_change + change
    mean_change = total_change / len(paired_changes)
```

改的是读者会卡住的地方：局部命名对应计算职责、缺失值规则写清楚、累加顺序为什么保留说明白。

没改的：`n − 1` 的分母、逐个累加的求值顺序、返回字段、异常文本、CLI 行为、以及那行“Zero is a real measurement”。

等价性不是嘴上说的：把局部变量统一改名后比较 AST，再用 5 个差分测试（含 4,369 组枚举数据、浮点特殊值、CLI 契约）跑原始实现与整理版逐一对齐。

## 三种模式

| 模式 | 你说什么 | 它做什么 |
|---|---|---|
| audit | “看看哪里难读”“先审查，别动代码” | 只读走读，不运行项目代码、不改文件与环境 |
| polish | “整理这份科研代码”（默认） | 最小必要编辑，按改动性质选择验证强度 |
| release | “准备复现交付包” | 在 polish 之上补足导航与运行说明，不发布、不上传 |

模式表不是触发关键词，也不附带额外权限。缺什么只问会改变结果的：目标文件、能否修改、论文在哪里。没有论文也能整理，只是把方法对应标成“未提供论文，尚未核对”。

## 最小有效编辑阶梯

对每个阅读障碍，停在第一个足以解决它的动作上，不必逐级做完：

1. 不妨碍理解 —— 不改。指不出具体收益的风格改动直接跳过。
2. 术语不清 —— 局部准确命名，或给论文符号一条简短映射，公共契约不动。
3. 意图不清 —— 补有证据的单位、假设、理由；删掉与代码矛盾或逐行复读的注释。理由未知写“待作者确认”，不替作者编科学依据。
4. 步骤混杂 —— 整理局部结构，清掉已核实无用的探索残留，保留警告和审计痕迹。
5. 仍然难懂 —— 才按计算职责拆成少量步骤，或合并已验证等价的重复逻辑。

简单脚本就保持一个脚本，清晰循环不必压成一行，不新增框架、依赖、CLI 或配置层。

阶梯跑在**理解之后**，不是替代理解：先写出“成对筛选 → 个体差值 → 样本统计”这样能核对的计算链，再决定动哪里。

不在削减范围内的：样本与缺失值规则、统计定义、单位与数值类型、求值顺序、随机流、训练/测试划分、图表的科学含义、公共 API 与异常。方法纠错、向量化、并行化、换库属于独立决定——发现疑似研究错误就单列，不混进润色。

## 按风险选检查

| 改动性质 | 最小充分行动 | 交付标注 |
|---|---|---|
| 只读审计 | 静态走读，遇相关科学定义再查不变量 | 不声称运行过 |
| 已确认无执行/工具影响的说明文字 | 对照实现与完整差异 | **V0** 静态核查，行为未执行验证 |
| 改名、拆分、合并等可执行改动 | 先建成功基线，再编辑，再回归 | **V1 / V2** 报告实际命令与容差 |
| 论文表图、正式复现物 | 整理前后与论文目标双向比较 | **V3** |
| 缺依赖、缺数据、缺算力，或基线本来就失败 | 该路径退回只读审计，独立组件可继续 | 逐路径标注，并说明集成缺口 |

一条容易被忽略的规则：docstring、类型注解、导入、装饰器、doctest、shebang / 编码声明、`#SBATCH` 一类的调度器注释，**不自动算纯文字**。它们可能被反射、测试、构建或产物消费；查不清消费方就按可执行改动处理。

不许用来“让检查变绿”的手段：更新 golden files、放宽断言、删掉失败测试、看到差异再放大容差。新差异没定位清楚之前，停止扩大改动范围，只撤销自己造成的相关修改，保留你原有的改动和原有结果。

回放里看到过这样一个差别：同样是改注释，优化前的流程读了两份完整参考资料并跑了两次数值比较；优化后的流程判定为 V0，没有读参考资料也没有重跑。同一次对照里另一个变体（可执行改名）仍然建立了基线并回归。这是单次观察，不是基准，且同轮总工具调用 19 → 21，因此不宣称整体提速。

## 交付只有三段

- **改了什么**：文件链接、具体的阅读收益、保留了哪些重要行为；没改动就说明原因，audit 明写“只读审计，未修改文件”。
- **验证了什么**：实际跑过的命令与方法、比较结果、判据与覆盖范围；只做过静态核查就标 V0。
- **还有什么未确认**：没跑的路径、集成风险、等你决定的方法问题或投稿事项。

不会给你“可以直接投稿”的认证。有限回归不是所有输入上的等价证明。

## 包含什么

```
skills/paper-code/
├── SKILL.md                       调用门禁、模式、边界、阶梯、交付
└── references/
    ├── scientific-invariants.md   科学不变量，按域分 9 节
    ├── verification.md            基线、比较定义、V0–V3、无法执行的边界
    └── evidence.md                7 份来源的依据矩阵
```

不变量分成 §1 样本/缺失/配对、§2 统计、§3 数值、§4 随机与机器学习、§5 时空与单位、§6 契约与工具消费、§7 图表、§8 notebook、§9 方法疑点；验证协议分成 §1 基线、§2 比较定义、§3 等级、§4 无法执行、§5 交付说明、§6 记录。

按需加载，不是每次整理都重读一遍文献，也不自动调用其他 Skill。README、LICENSE 与 assets/ 在仓库根目录，不在 Skill 包内：包里只有这 4 个 Markdown 文件。

## 安装

技能在仓库的 `skills/paper-code/`。两种方式，任选其一。

**跨 Agent 共享一份**（`skills` CLI 默认用符号链接，不重复拷贝）：

```powershell
skills add Rimagination/paper-code --global --agent '*' --skill paper-code --yes
```

**手动放入技能目录**：克隆仓库，把 `skills/paper-code/` 整个复制进去。

```bash
git clone https://github.com/Rimagination/paper-code.git
cp -r paper-code/skills/paper-code ~/.qoder/skills/
```

把 `~/.qoder/skills/` 换成你的宿主目录，例如 `~/.claude/skills/`、`~/.codex/skills/`、`~/.config/opencode/skills/`、`~/.zcode/skills/`，最终得到 `<skills>/paper-code/SKILL.md`。

装完检查入口确实指向同一份规范副本：

```powershell
Get-Item "$env:USERPROFILE\.qoder\skills\paper-code" | Select-Object FullName, LinkType, Target
```

Windows 拒绝创建符号链接时，打开开发者模式或用管理员 PowerShell；确实需要独立副本时才用 `--copy`。

只需要一份规则文件的宿主，可以直接把 `skills/paper-code/SKILL.md` 当规则加载；它没有可执行代码、没有网络调用、没有配置文件。装好后重启会话或执行 `/skills reload`，用 `/paper-code` 调用。

## 用法

| 说法 | 结果 |
|---|---|
| `/paper-code` + “整理这份脚本” | polish：建基线 → 最小编辑 → 回归 → 三段交付 |
| `/paper-code` + “只看哪里难读” | audit：只读，不改任何东西 |
| “用 paper-code 准备复现说明” | release：补导航与命令，不发布 |
| “提高这段代码的可读性” | 不启用本 Skill，走普通任务流程 |

调用只覆盖本次指定的任务。论文正文润色、隐瞒生成来源、普通产品开发不在它的工作范围内。

## FAQ

**它会不会自动跳出来，拖慢我？**
不会。未显式点名时它不启动，误加载也只停在调用门禁一节，不去读参考资料或跑额外检查。

**只改一行注释也要跑一遍数值？**
不需要。确认过没有执行与工具消费影响的文字改动标 V0：静态核查，行为未执行验证。判断不了有没有影响，就按可执行改动处理。

**原代码本身算错了怎么办？**
单列为方法问题，写清楚位置和后果，等你单独授权再改。润色不夹带纠错。

**会把循环改成向量、换个统计库吗？**
不会，除非你把它当成独立任务批准下来。默认保留运算次序、类型、索引、异常和副作用。

**没有论文能用吗？**
能。照样整理代码，方法对应会标成“未提供论文，尚未核对”，不编公式和表图编号。

**能声称符合某个期刊的要求吗？**
未指定期刊时只跑通用可复现性检查；指定了也要当场核实该刊当前规则。许可证、署名、AI 使用声明、匿名审稿合规由作者和期刊决定，它不擅自处理。

**“搜不到引用”是不是就可以删？**
不是。还要看 CLI、配置字符串、动态导入、notebook、文档和外部使用者。

**AI 写的代码更差吗？**
不预设。同样也不预设它更好——判断依据只有两个：读者能不能看懂，行为变了没有。

## 限制与未验证

如实写在这里：

- 没有做过人类理解度实验，没有可读性评分、阅读时间或提速百分比。
- 没有做过 token / 延迟基准；上文那次对照是单次回放，总工具调用没有下降。
- 没有在任何真实研究仓库或真实论文产物上端到端跑过，验证用的是合成夹具。
- 未跨宿主测试“不会被自动调用”这条在实际宿主里的表现，只在当前宿主观察到描述更新生效。
- 有限输入空间的回归通过，不等于任意输入上的等价证明。
- 依据矩阵里的期刊与复现要求为 2026-11 前后采集，使用时需按当刊当期规则复核。

## 许可

[MIT](LICENSE)。可自由使用、修改与再分发，保留版权与许可声明即可。

许可由作者选定；Skill 本身不替你决定署名、AI 使用声明或匿名审稿合规。

---

<p align="center">
  <sub>他把注释删了，把分母留着。<br><a href="README.md">简体中文</a> · <a href="README.en.md">English</a></sub>
</p>
