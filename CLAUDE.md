# CLAUDE.MD — 顶级经济学论文写作工作台

**项目定位：** 经济学博士科研助手（AI-RA）与严格学术合著者
**目标期刊：** AER、QJE、Econometrica、REStud 等顶级期刊
**分支：** main

---

## 核心原则

- **计划优先** —— 非简单任务前进入计划模式；计划保存至 `quality_reports/plans/`
- **验证收尾** —— 每个任务结束后编译/运行并确认输出
- **单一真实来源** —— `Code/` 中的代码是权威来源；`Paper/Tables/` 从代码输出派生
- **质量门** —— 主论文目录低于 90/100 不提交；探索目录阈值为 60/100
- **[LEARN] 标签** —— 被纠正时，将 `[LEARN:类别] 错误 → 正确` 保存到 MEMORY.md
- **零容忍 p-hacking** —— 不容忍数据挖掘、选择性报告或事后假设

---

## 文件夹结构

```
[YOUR-PROJECT]/
├── CLAUDE.md                    # 本文件
├── .claude/                     # 规则、技能、代理、钩子
├── Paper/                       # 主论文 LaTeX 文件
│   ├── main.tex                 # 主文档
│   ├── Tables/                  # 自动生成的 LaTeX 表格
│   ├── Figures/                 # 图形（PDF/EPS/SVG）
│   └── Appendix/                # 数学附录与稳健性检验
├── Code/                        # 所有实证代码
│   ├── R/                       # R 脚本
│   ├── Stata/                   # Stata do 文件
│   └── Python/                  # Python 脚本
├── Data/                        # 数据（原始 + 清洗后）
│   ├── raw/                     # 原始数据（只读）
│   └── clean/                   # 清洗后数据（RDS/dta/csv）
├── explorations/                # 实证试错沙盒（60/100 标准）
├── empirical_tests/             # 快速稳健性检验
├── Bibliography.bib             # 集中参考文献库
├── quality_reports/             # 计划、会话日志、复现报告
└── master_supporting_docs/      # 参考文献 PDF
```

---

## 核心命令

```bash
# LaTeX 编译（3 次，XeLaTeX）
cd Paper && xelatex -interaction=nonstopmode main.tex
bibtex main
xelatex -interaction=nonstopmode main.tex
xelatex -interaction=nonstopmode main.tex

# 运行 R 脚本
Rscript Code/R/filename.R

# 运行 Stata（批处理模式）
stata -b do Code/Stata/filename.do
```

---

## 质量阈值

| 分数 | 门槛 | 含义 |
|-------|------|---------|
| 60 | 探索提交 | 沙盒代码，结果可信即可 |
| 80 | 工作论文 | 可分享给合作者 |
| 90 | 投稿 | 顶级期刊标准 |
| 95 | 卓越 | 追求目标 |

---

## 代理快速参考

| 代理 | 职责 |
|---------|-------------|
| `econometrics-reviewer` | 审查内生性、SE 计算、识别策略 |
| `typesetter` | 执行 LaTeX 表格与数学排版标准 |
| `reviewer-2` | 对引言与识别策略进行对抗性 QA |
| `r-reviewer` | R 代码质量与可重现性审查 |
| `verifier` | 端到端验证：代码运行、表格生成、编译 |

---

## 技能快速参考

| 命令 | 功能 |
|---------|-------------|
| `/compile-latex [file]` | 3 次 XeLaTeX + bibtex |
| `/proofread [file]` | 语法/拼写/溢出审查 |
| `/review-r [file]` | R 代码质量审查 |
| `/validate-bib` | 交叉核对引用 |

---

## 当前项目状态

| 章节 | 代码 | 表格 | 状态 |
|---------|--------|--------|-------------|
| [待填写] | | | |

---

## 计量经济学标准（不可妥协）

- 所有回归必须报告聚类标准误（除非有充分理由）
- 工具变量必须通过弱工具检验（F > 10，或 Montiel-Olea-Pflueger 检验）
- 双重差分必须检验平行趋势（事件研究图）
- 所有主要结果必须有稳健性检验（不同 SE 聚类、样本、规格）
- 禁止在看到结果后修改假设（HARKing）
