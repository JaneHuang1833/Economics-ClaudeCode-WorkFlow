# 经济学博士全自动科研助手：工作流指南

> 本指南说明如何使用本仓库的 Claude Code 配置，将 AI 作为你的全职科研助手（AI-RA）和严格的学术合著者，目标是产出符合 AER、QJE、Econometrica 等顶级期刊标准的论文。

---

## 目录

1. [这是什么？](#1-这是什么)
2. [快速开始](#2-快速开始)
3. [文件夹结构](#3-文件夹结构)
4. [核心工作流程](#4-核心工作流程)
5. [规则系统](#5-规则系统)
6. [三委员会审查制度](#6-三委员会审查制度)
7. [质量门](#7-质量门)
8. [常用命令](#8-常用命令)
9. [内存系统](#9-内存系统)
10. [最佳实践](#10-最佳实践)

---

## 1. 这是什么？

本仓库是一套为**经济学博士**量身定制的 Claude Code 配置，将 AI 从"教学助手"升级为"全职科研助手"。

### 核心理念

| 旧模式（教学助手） | 新模式（科研助手 AI-RA） |
|---|---|
| 制作 Beamer 幻灯片 | 撰写顶级期刊论文 |
| 同步幻灯片到网站 | 同步代码输出到 LaTeX 表格 |
| 解释概念给学生 | 审查识别策略的严谨性 |
| 80/100 质量门 | 90/100 质量门（投稿标准） |
| 容忍模糊 | 零容忍 p-hacking |

### 适合谁？

- 正在写作实证经济学论文的博士生
- 需要处理大量 R/Stata 代码和 LaTeX 表格的研究者
- 希望 AI 帮助执行计量经济学标准、而不只是写代码的用户

---

## 2. 快速开始

### 前提条件

```bash
# 必须安装
- Claude Code CLI
- R（含 fixest、modelsummary 等包）
- XeLaTeX（用于编译论文）
- Stata（可选）
```

### 克隆并配置

```bash
git clone [your-repo-url]
cd [your-project]

# 填写项目信息
# 编辑 CLAUDE.md 中的占位符
# 编辑 .claude/rules/knowledge-base-template.md 填入论文知识库
```

### 第一次使用

```bash
claude  # 启动 Claude Code

# 告诉 Claude 你的论文主题
# "我在研究[主题]，使用[识别策略]，数据来自[数据源]"
```

---

## 3. 文件夹结构

```
your-project/
├── CLAUDE.md                    # AI 配置核心文件（必读）
├── MEMORY.md                    # 跨会话学习记录
├── WORKFLOW_GUIDE.md            # 本文件
│
├── Paper/                       # 主论文
│   ├── main.tex                 # 主文档入口
│   ├── Tables/                  # 自动生成的 LaTeX 表格
│   ├── Figures/                 # 图形（PDF/EPS）
│   └── Appendix/                # 数学附录与稳健性检验
│
├── Code/                        # 所有实证代码（真实来源）
│   ├── R/                       # R 脚本
│   ├── Stata/                   # Stata do 文件
│   └── Python/                  # Python 脚本（可选）
│
├── Data/                        # 数据
│   ├── raw/                     # 原始数据（只读，不提交）
│   └── clean/                   # 清洗后数据（RDS/dta）
│
├── explorations/                # 实证沙盒（60/100 标准）
├── empirical_tests/             # 快速稳健性检验
├── Bibliography.bib             # 集中参考文献库
│
├── quality_reports/             # 质量管理
│   ├── plans/                   # 任务计划
│   ├── session_logs/            # 会话日志
│   └── merges/                  # 合并质量报告
│
├── master_supporting_docs/      # 参考文献 PDF
│
└── .claude/                     # Claude Code 配置
    ├── rules/                   # 行为规则（自动加载）
    ├── agents/                  # 专业代理
    ├── skills/                  # 可调用技能
    └── hooks/                   # 自动化钩子
```

### 单一真实来源原则

```
Code/R/*.R  ──运行──▶  Data/clean/*.rds
                  └──生成──▶  Paper/Tables/*.tex
                  └──生成──▶  Paper/Figures/*.pdf

永远不要手动编辑 Paper/Tables/ 中的数字。
始终从代码重新生成。
```

---

## 4. 核心工作流程

### 4.1 计划优先（Plan-First）

对于任何非简单任务，Claude 会自动进入计划模式：

```
用户提出任务
    │
    ▼
Claude 进入计划模式
    │  读取 CLAUDE.md + MEMORY.md
    │  起草计划
    │  保存到 quality_reports/plans/YYYY-MM-DD_description.md
    ▼
用户审批计划
    │
    ▼
Claude 执行（编排器模式）
    │  实施 → 验证 → 审查 → 修复 → 评分
    ▼
达到质量门 → 报告完成
```

**何时跳过计划：** 单行修复、明确的小任务。

### 4.2 编排器循环

计划批准后，Claude 自主执行：

```
实施
  ↓
验证（代码运行 + 论文编译）
  ↓  失败 → 修复 → 重新验证
审查（调用相关代理）
  ↓
修复（严重 → 主要 → 次要）
  ↓
重新验证
  ↓
评分
  ↓
分数 ≥ 阈值？ → 是 → 报告完成
              → 否 → 返回审查（最多 5 轮）
```

### 4.3 实证沙盒（Fast-Track）

对于初步想法和探索性分析，使用轻量级流程：

```bash
# 创建沙盒
mkdir -p explorations/my_idea/{code,temp_data,raw_output}

# 工作在沙盒内，60/100 标准
# 不需要完美的 LaTeX 表格
# 只需要：代码跑通 + 核心系数 + SESSION_LOG.md 记录

# 决策：升级到 Code/（90/100）或归档
```

---

## 5. 规则系统

`.claude/rules/` 下的文件会根据你正在编辑的文件自动加载。

| 规则文件 | 触发路径 | 核心内容 |
|---------|---------|---------|
| `sync-code-to-latex.md` | `Code/**`, `Paper/Tables/**` | 代码→表格同步协议 |
| `anti-phacking.md` | `Code/**`, `Paper/**` | 禁止 p-hacking 与 HARKing |
| `quality-gates.md` | `Code/**`, `Paper/**` | 评分标准与阈值 |
| `r-code-conventions.md` | `**/*.R` | R 代码规范 |
| `replication-protocol.md` | `Code/**` | 复现优先协议 |
| `single-source-of-truth.md` | `Code/**`, `Paper/**` | 单一真实来源执行 |
| `verification-protocol.md` | `Code/**`, `Paper/**` | 任务完成验证 |
| `exploration-fast-track.md` | `explorations/**` | 沙盒快速通道 |
| `plan-first-workflow.md` | 全局 | 计划优先工作流程 |

### 关键规则：代码→表格同步

每次修改生成结果的代码后，Claude 会**自动**：
1. 重新运行代码
2. 更新 `Paper/Tables/` 中的对应表格
3. 验证数字一致性
4. 编译论文确认无错误

### 关键规则：零 p-hacking

Claude 会拒绝以下操作：
- 看到结果后修改假设（HARKing）
- 反复尝试规格直到 p < 0.05
- 选择性报告显著结果
- 通过删除"异常值"使结果显著

---

## 6. 三委员会审查制度

`.claude/agents/` 下有三个核心审查代理，模拟顶级期刊的审稿流程。

### 计量经济学审查员（econometrics-reviewer）

**职责：** 审查识别策略的严谨性

检查内容：
- 识别假设是否明确陈述
- 工具变量：相关性（F > 10）和排他性限制
- 双重差分：平行趋势假设 + 事件研究图
- 聚类标准误层级是否正确
- 内生性来源和方向

**调用方式：**
```
请用 econometrics-reviewer 审查我的第三章识别策略
```

### 顶级期刊排版编辑（typesetter）

**职责：** 执行 AER/QJE/Econometrica 排版标准

检查内容：
- 表格：`booktabs`、无竖线、`threeparttable`
- 标准误在括号内紧跟系数
- 数学环境：`equation`、`align`
- 引用：`\citet{}` vs `\citep{}` 正确使用

**调用方式：**
```
请用 typesetter 审查 Paper/Tables/tab_main.tex
```

### 匿名审稿人 2（reviewer-2）

**职责：** 对抗性 QA，找出逻辑漏洞

检查内容：
- 贡献声明是否过度夸大
- 是否遗漏重要相关文献
- 替代解释是否被排除
- 经济显著性是否被讨论

**调用方式：**
```
请用 reviewer-2 审查我的引言和识别策略部分
```

---

## 7. 质量门

| 分数 | 门槛 | 含义 |
|-------|------|---------|
| 60/100 | 探索提交 | 沙盒代码，结果可信即可 |
| 80/100 | 工作论文 | 可分享给合作者 |
| 90/100 | 投稿 | 顶级期刊标准 |
| 95/100 | 卓越 | 追求目标 |

### 扣分项（主要）

**论文 LaTeX：**
- 表格数字与代码不一致：-50
- 使用竖线或 `\hline`：-10
- 标准误格式错误：-10

**R/Stata 代码：**
- 聚类 SE 计算错误：-30
- 硬编码绝对路径：-20
- 缺少 `set.seed()`：-15

### 执行

- 分数 < 80：阻止提交，列出阻塞问题
- 分数 < 90：允许提交工作论文版本，发出警告
- 用户可提供理由覆盖

---

## 8. 常用命令

### 编译论文

```bash
cd Paper
xelatex -interaction=nonstopmode main.tex
bibtex main
xelatex -interaction=nonstopmode main.tex
xelatex -interaction=nonstopmode main.tex
```

### 运行代码

```bash
Rscript Code/R/main_regression.R
stata -b do Code/Stata/analysis.do
```

### 技能命令

| 命令 | 功能 |
|---------|-------------|
| `/compile-latex main` | 3 次 XeLaTeX + bibtex |
| `/proofread Paper/main.tex` | 语法/拼写/格式审查 |
| `/review-r Code/R/main_regression.R` | R 代码质量审查 |
| `/validate-bib` | 交叉核对引用 |

---

## 9. 内存系统

### 两层内存

**MEMORY.md（已提交，跨会话）**

记录通用学习成果：
```markdown
[LEARN:econometrics] feols() 的 cluster 参数需要用 ~var 格式
[LEARN:latex] threeparttable 需要在 \begin{table} 后立即使用
[LEARN:workflow] 先跑描述性统计再跑主回归，更容易发现数据问题
```

**`.claude/state/personal-memory.md`（gitignore，本地）**

记录机器特定配置：
```markdown
[LEARN:paths] 数据在 ~/Dropbox/Data/project_name/
[LEARN:stata] 本机 Stata 路径 /usr/local/stata17/stata
```

### 何时更新 MEMORY.md

- 被 Claude 纠正了某个计量方法
- 发现了新的 R 包用法
- 解决了一个反复出现的问题

---

## 10. 最佳实践

### 开始新任务

```
# 好的方式
"我想为主回归添加异质性分析，按性别分组，
 使用与主回归相同的控制变量和聚类层级"

# 不好的方式
"帮我改进回归"
```

### 处理不显著结果

不显著的结果**不是失败**。告诉 Claude：
```
"主变量在加入州固定效应后不再显著，
 请帮我分析可能的原因并记录到 SESSION_LOG.md"
```

### 稳健性检验

在主结果完成后，要求 Claude 系统性地运行稳健性检验：
```
"请为表2的主结果运行以下稳健性检验：
 1. 不同聚类层级（个人/县/州）
 2. 去掉前后5%的样本
 3. 加入/去掉控制变量"
```

### 会话结束

每次会话结束时，Claude 会自动更新会话日志。你也可以主动要求：
```
"请更新会话日志，记录今天的进展和未解决的问题"
```

---

## 附录：计量经济学不可妥协标准

以下标准在任何情况下都不得妥协：

1. **聚类标准误** —— 默认在处理单元层面聚类
2. **工具变量** —— 必须通过弱工具检验（F > 10 或 Montiel-Olea-Pflueger）
3. **双重差分** —— 必须有事件研究图支撑平行趋势
4. **多重检验** —— 同时检验多个假设时必须校正
5. **预先登记** —— 主要假设在看数据前登记到 `quality_reports/`
6. **完整报告** —— 所有稳健性检验（包括不显著的）必须在附录中报告

---

*本指南基于 Claude Code 配置自动生成。如有更新，请同步修改 `CLAUDE.md`。*
