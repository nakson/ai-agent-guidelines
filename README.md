# ai-agent-guidelines

> 中文可称：**AI Agent 使用指南库**  
> 本机路径：`/root/ai-agent-guidelines`

跨工具共用的 Agent 可读规范与踩坑沉淀。正文只维护本库；业务仓用短指针挂钩。

**目的**：Agent 按任务读对口材料 → 尽快产出符合团队标准的结果，少胡写、少重复踩坑。

---

## 索引：什么任务读什么

| 任务 | 先读 | 再读（按需） |
|------|------|----------------|
| 任意任务 | `ai/constitution.md`（扫一眼） | — |
| 写/改技术文档、开篇、一页纸 | `ai/tech-writing.md` | — |
| 写/改业务代码、接口、重构 | `ai/coding.md` | 对口 `ai/pitfalls/*` |
| 动 Tabs / effect 依赖 / 页签回调 | `ai/pitfalls/react-effect-tabs.md` | `ai/coding.md` |
| 改 UI 文案 / i18n | `ai/pitfalls/i18n-csv-only.md` | 业务仓 i18n 路径规则 |
| 不确定读哪 | 本索引 + `ai/constitution.md` | 用主题搜 `ai/` 与 `pitfalls/` |

原则：**按需触发，不全量灌**。不要把整库贴进一次对话。

---

## 目录

```
ai-agent-guidelines/
├── README.md                 # 索引：什么任务读什么（本文件）
├── ai/
│   ├── constitution.md       # 极短：永远遵守的硬约束（口味底线）
│   ├── tech-writing.md       # 写文档
│   ├── coding.md             # 写代码通例
│   └── pitfalls/             # 一坑一篇：错因 → 必须/禁止 → 反面→正面 → 自检
│       ├── react-effect-tabs.md
│       └── i18n-csv-only.md
└── hooks/                    # 各工具短指针模板（拷进业务仓后改路径）
    ├── AGENTS.md.snippet
    ├── CLAUDE.md.snippet
    ├── cursor-rule.mdc.snippet
    └── copilot-instructions.md.snippet
```

---

## 方向与原则

方向对：把标准与坑蒸馏成**可触发、可检查**的机构记忆。  
走歪信号：规范变厚，同一类错仍反复出现。

| 原则 | 含义 |
|------|------|
| 一份权威正文 | 通则与坑只在本库；业务仓不复制全文 |
| 短指针挂钩 | 工具入口只写相对路径 |
| 按需触发 | 文档 / 代码 / 单坑分开挂；禁止事事 always-on |
| 可检查 | 必须 / 禁止 / 自检；优先反面 → 正面 |
| 靠 Git 对齐 | 不以本机绝对路径或个人全局规则当权威 |
| 能升就升 | 能变测试 / lint 的升自动化 |

---

## 怎么使用

### 1. 维护本库

a) 通例进专章；一坑一篇进 `pitfalls/`；跨任务硬约束才进 `ai/constitution.md`（保持极短）。  
b) 结构：何时使用 → 必须 / 禁止 → 反面 → 正面 → 自检。  
c) 能自动化的同时补测试；只改本库时业务仓指针路径通常不用动。

### 2. 业务仓引进（三选一）

| 方式 | 做法 | 引进后路径示例 |
|------|------|----------------|
| Submodule | `git submodule add <URL> docs/ai-agent-guidelines` | `docs/ai-agent-guidelines/ai/tech-writing.md` |
| 发布拷贝 | CI 按 tag 同步并开 PR | 同上 |
| 本机 symlink | `ln -s /root/ai-agent-guidelines <仓>/docs/ai-agent-guidelines` | 同上；**指针勿写 `/root/...`** |

### 3. 挂钩（项目级，引进后做一次）

从 `hooks/` 拷 snippet 到业务仓，改成上表相对路径：

| 工具 | 挂到 |
|------|------|
| 通用 | `AGENTS.md` |
| Claude Code | `CLAUDE.md` 一小节 |
| Cursor | `.cursor/rules/*.mdc`（按 `globs` 指到专章） |
| Copilot | `.github/copilot-instructions.md` |

默认应用 = **每个业务项目**完成引进 + 挂钩，不是只在 server 上配全局 Agent。

### 4. 派活

说清任务与验收；`@` 对口文件或依赖 globs；收尾过该篇自检并跑相关测试。

### 5. 分工

| 内容 | 放哪 |
|------|------|
| 跨项目通例与坑 | 本库 |
| 产品术语、路径特例 | 业务仓短文件 |
| `globs` / `alwaysApply` | 业务仓工具适配层 |

---

## 负向边界

| 避免 | 结果 |
|------|------|
| 一本巨型 MD 全塞 | 规则稀释、执行变差 |
| 只宣言不写坑 | 旧错重犯 |
| 事事 alwaysApply | 规则互扰 |
| 聊天长文不蒸馏 | Agent 用不了 |
| 只靠文档不验收 | 仍胡写 |
| 规范当银弹取消人审 | 高风险失控 |
| 业务仓复制全文 / 绝对路径指针 | 漂移或换机失效 |
| `ai/constitution.md` 无限加长 | 失去「永远遵守」的可执行性 |

---

## 新专章 / 新坑模板

```markdown
# <主题>

## 何时使用
…

## 必须
1. …

## 禁止
- …

## 反面 → 正面
| 旧 | 新 |
|----|----|
| …  | …  |

## 自检
- [ ] …
```
