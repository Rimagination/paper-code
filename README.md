<div align="center">
  <img src="./assets/logo.png" alt="paper-code" width="96" />
  <h1>paper-code</h1>

  <a href="./skills/paper-code/SKILL.md"><img alt="Agent Skill" src="https://img.shields.io/badge/agent%20skill-4%20markdown%20files-0F766E?style=flat-square" /></a>
  <a href="./LICENSE"><img alt="License" src="https://img.shields.io/badge/license-MIT-0F766E?style=flat-square" /></a>

  点名才动手。把科研代码交给 Agent，它整理成同行能读懂、能核查的实现，并核对计算行为没有变。

  <sub><strong>简体中文</strong> · <a href="README.en.md">English</a></sub>
</div>

<p align="center">
  <a href="#这是什么">这是什么</a> ·
  <a href="#能做什么">能做什么</a> ·
  <a href="#开始使用">开始使用</a> ·
  <a href="#你会得到什么">你会得到什么</a> ·
  <a href="#使用边界">使用边界</a> ·
  <a href="#进阶参考">进阶参考</a>
</p>

## 这是什么

**paper-code 是面向 AI Agent 的科研代码整理 Skill。**给它一份分析脚本、一个 notebook 或一个研究仓库；它先写出能核对的计算链，再按最小必要编辑消除阅读障碍，并比较整理前后的行为，把结果整理为改动说明、验证记录和待确认清单。

它只在显式调用时启动：`/paper-code`、宿主 Skill 选择器，或明确说“用 paper-code 处理这个任务”。启动后按意图选择 audit（只读）、polish（默认整理）、release（复现交付）三种模式。

> 没有论文也能用：方法对应会标成“未提供论文，尚未核对”，不会编造公式、单位或表图编号。

### 适合谁

- 准备投稿或分享代码，希望同行能读懂、能复现的研究者和学生。
- 需要确认“整理没有改变科学结果”的作者、合作者与审稿人。
- 想把科研代码审查与整理接入 AI Agent 工作流的实验室。

如果你要的是改算法、换统计库、向量化或修正研究错误，请把它作为独立任务提出；paper-code 的润色不夹带方法变更。普通的“提高可读性”请求也不会启用它。

### 已核查的事实

- 包内只有 4 个 Markdown 文件：主规则 79 行，加三份按需加载的参考。
- 合成配对观测夹具上 5 个差分测试通过，其中有限输入空间枚举 4,369 组数据。
- 31 个静态场景由独立审阅者完整读 Skill 后走读判定。
- 未测：token / 延迟基准、人类理解度实验、真实研究仓库端到端、跨宿主自动调用表现。

## 能做什么

| 任务 | 输入示例 | 结果 |
|---|---|---|
| **只读审查** | “看看哪里难读，先别改” | 阅读障碍与方法疑点清单；不改文件、不运行项目代码。 |
| **最小整理** | 一份分析脚本或 notebook | 命名、注释与局部结构的必要编辑，附基线对比与验证等级。 |
| **复现交付** | 准备投稿的代码目录 | 在整理之上补导航、运行命令与验证状态；不发布、不上传。 |
| **行为核对** | 整理前后的同一组输入 | 差分比较与事先约定的容差；V0–V3 如实标注覆盖范围。 |
| **疑点单列** | 疑似统计定义或单位错误 | 位置、后果与待作者确认项；不混入润色。 |

每个阅读障碍停在第一个足以解决它的动作上：不妨碍理解就不改，清晰循环不压成一行，简单脚本不建包架构。

## 开始使用

### Agent Skill

把下面这句话发给 Codex、Claude Code、Cursor、Qoder 或 ZCode 等 Agent：

```text
帮我安装这个 Skill：https://github.com/Rimagination/paper-code
```

安装后重启会话或执行 `/skills reload`，然后点名调用，例如：

```text
/paper-code 整理这份分析脚本：先建基线，再改，最后告诉我验证了什么。
```

只想审查时说“用 paper-code 只读审查这份代码”，它不会改任何文件。

### 手动或共享安装

跨 Agent 共用一份（`skills` CLI 默认符号链接）：

```powershell
skills add Rimagination/paper-code --global --agent '*' --skill paper-code --yes
```

或克隆后把 `skills/paper-code/` 复制进宿主技能目录（`~/.claude/skills/`、`~/.qoder/skills/`、`~/.codex/skills/`、`~/.config/opencode/skills/`、`~/.zcode/skills/`）：

```bash
git clone https://github.com/Rimagination/paper-code.git
cp -r paper-code/skills/paper-code ~/.qoder/skills/
```

它没有可执行代码、没有网络调用、没有配置文件；只读规则文件的宿主可直接加载 `skills/paper-code/SKILL.md`。

## 你会得到什么

- **改了什么：**文件链接、具体的阅读收益、保留的重要行为；没改动会说明原因。
- **验证了什么：**实际跑过的命令与方法、比较结果、判据与覆盖；仅静态核查标 V0。
- **还有什么未确认：**未运行的路径、集成风险、等你决定的方法问题或投稿事项。
- **不变的契约：**返回字段、CLI、默认值、异常、路径与图表的科学含义保持原样。
- **按需的参考：**科学不变量与验证协议按涉及的分节加载，不每次重读文献。

## 使用边界

- 未显式点名不启动；误加载只停在调用门禁，不读参考、不跑额外检查。
- 不预设 AI 代码更差，也不按行数、“AI 味”或个人风格重写。
- 方法纠错、向量化、并行化、换库、改精度或随机流是独立决定，需另行授权。
- 基线失败或缺依赖、数据、算力的路径退回只读审计；独立组件可继续，集成缺口会说明。
- 不更新 golden files、不放宽断言、不删失败测试来让检查变绿；新差异未定位前停止扩大改动。
- 有限回归不是任意输入上的等价证明；不给“可直接投稿”认证。
- 不自动提交、推送、发布或上传；许可证、署名与 AI 使用声明由作者决定。
- 未指定期刊时只做通用可复现性检查；指定期刊也需当场核实其当前规则。

## 进阶参考

- [主规则 SKILL.md](skills/paper-code/SKILL.md)：调用门禁、模式、边界与交付格式。
- [科学不变量](skills/paper-code/references/scientific-invariants.md)：样本、统计、数值、随机、时空、契约、图表、notebook。
- [验证协议](skills/paper-code/references/verification.md)：基线、比较定义、V0–V3、无法执行时的边界。
- [研究依据](skills/paper-code/references/evidence.md)：7 份来源的依据矩阵与适用性说明。
- [Issues](https://github.com/Rimagination/paper-code/issues)：报告问题或提出需求。
- 许可证：[MIT](LICENSE)。
