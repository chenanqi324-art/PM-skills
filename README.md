# Complex Flow Design Map

一个用于复杂产品业务流程、原型流程和单页面状态梳理的 Codex Skill。

它会把业务统一组织为：

```text
Module
→ Flow
→ Node
→ State / Action
→ Transition
→ Target
→ UI
```

适合产品经理、交互设计师和业务设计人员，用来整理长流程原型、页面状态、异常分支、跨流程跳转及设计稿清单。

## 核心能力

- 梳理完整的端到端业务流程
- 展开单个页面的 State、Action、Trigger 和 UI
- 区分 State 与独立 Flow，避免流程过度拆分
- 区分 PA、AS、State Action 和 System Action
- 生成统一、稳定的业务与 UI 编码
- 输出结构化主表、分支清单和设计稿清单
- 在已有流程上增量修改，并保持原编码稳定
- 审计遗漏状态、错误拆分、重复 UI 和未闭环分支

## 文件说明

当前下载包包含：

```text
complex-flow-design-map-SKILL.md   Skill 正文
README.md                          使用说明
```

安装时建议整理为以下目录结构：

```text
complex-flow-design-map/
├── SKILL.md
└── README.md
```

也就是说，需要把 `complex-flow-design-map-SKILL.md` 重命名为 `SKILL.md`，再与本 README 一起放进 `complex-flow-design-map` 文件夹。

## 安装方式

将整理后的 `complex-flow-design-map` 文件夹放入 Codex 的 Skills 目录：

```text
~/.codex/skills/complex-flow-design-map/
```

安装完成后的完整路径应类似：

```text
~/.codex/skills/complex-flow-design-map/SKILL.md
```

重新开启任务后，即可通过技能名调用。

## 使用方式

可以明确指定技能：

```text
使用 $complex-flow-design-map，帮我梳理这个原型流程。
```

也可以直接描述任务，由 Codex 根据输入自动判断处理模式。

## 五种工作模式

### FLOW_MAP

适用于完整原型长图、多张连续页面、完整流程图或端到端业务描述。

示例：

```text
使用 $complex-flow-design-map，梳理这套开户注册原型的完整流程，补充主要分支和设计稿清单。
```

默认输出：

- Business Tree
- Structured Master Table
- Trigger / Branch List
- Missing / Potential States
- UI Checklist

### PAGE_EXPAND

适用于单张页面或指定 Node 的深入分析。

示例：

```text
使用 $complex-flow-design-map，分析这个身份证上传页面可能存在的 State、Action、Trigger 和异常情况。
```

默认输出：

- Node Position
- Node Structure
- Local Structured Table
- Branch Analysis
- UI Checklist

### UPDATE

适用于在已有流程结构上新增或修改规则。

示例：

```text
在现有 B2 流程中增加“OCR服务不可用”的处理逻辑。保持已有编码，只输出受影响的节点和 UI。
```

### AUDIT

适用于检查流程完整性和结构合理性。

示例：

```text
审计这份流程表，检查是否存在 State 与 Flow 拆分错误、编码冲突、不可达 Node、重复 UI 或未闭环分支。
```

### MIXED

适用于既需要理解完整流程，又需要重点展开其中一个页面的情况。

示例：

```text
先理解整套认证流程，再重点展开 B2.1 身份证上传页面；其他节点只保留主干结构。
```

## 输入建议

为了得到更稳定的结果，输入中可以包含：

- 原型长图、页面截图或流程图
- 当前业务目标和用户入口
- 已存在的 Module、Flow、Node 编码
- 页面之间的跳转关系
- 已确认的异常规则和业务限制
- 希望重点分析的页面或分支
- 当前已有的流程表

已有编码时，应明确说明“沿用已有编码”，避免模型把建议编号误当成正式编号。

## 输出模型

### 层级结构

```text
Module
└── Flow
    └── Node
        ├── States
        ├── Page Actions
        ├── Action Slots
        ├── State Actions
        └── System Actions
             ↓
         Transition
             ↓
           Target
             ↓
             UI
```

State 和 Action 是 Node 下的并列维度，不应默认理解为严格的父子关系。

### Action 类型

| 类型 | 含义 | 典型用途 |
|---|---|---|
| PA | Page Action | 页面长期存在的固定操作入口 |
| AS | Action Slot | 位置固定，但文案、状态或行为随 State 改变的操作位 |
| A | State Action | 只在特定 State 下出现的操作 |
| SA | System Action | 系统自动识别、更新、跳转或路由 |

### Transition 类型

Skill 使用以下标准 Transition：

- Stay
- Continue
- Submit
- Trigger
- Route
- Resume
- Retry
- Return
- Wait
- Show
- End

Action 与 Target 会分开描述，便于维护和检查。

### 置信度标记

对于输入中没有明确给出的业务内容，Skill 会使用：

- `CONFIRMED`：输入或原型中已经明确
- `INFERRED`：根据业务和交互合理推断
- `TO_CONFIRM`：会影响业务路径，但信息不足，需要确认

## 编码速查

| 对象 | 格式 | 示例 |
|---|---|---|
| Module | 大写字母 | `B` |
| Flow | Module + 序号 | `B2` |
| Node | Flow + 节点序号 | `B2.1` |
| State | Node + `Sxx` | `B2.1-S03` |
| Page Action | Node + `PAxx` | `B2.1-PA01` |
| Action Slot | Node + `ASxx` | `B2.1-AS01` |
| State Action | Node + State + `Axx` | `B2.2-S20-A01` |
| System Action | Node + State + `SAxx` | `B2.2-S02-SA01` |
| UI | `UI-Node-State-TypeNo` | `UI-B2.2-S20-M01` |

推荐 UI Type：

- `P`：Page
- `M`：Modal
- `S`：Sheet
- `T`：Toast
- `I`：Inline

## 首轮测试建议

建议在一个没有历史业务上下文的新任务中做两次测试。

### 测试一：完整流程

提供一张完整原型长图或一段端到端业务说明：

```text
使用 $complex-flow-design-map，帮我梳理这个原型的完整业务流程。
```

重点检查：

- 是否识别出 Module、Flow 和 Node
- 是否区分主流程与 Branch Flow
- 是否把重复页面识别为 State 或 Variant
- 是否生成结构化主表和 UI Checklist

### 测试二：单个页面

只提供一张页面截图：

```text
使用 $complex-flow-design-map，分析这个页面可能存在的状态、操作和分支。
```

重点检查：

- 是否只深入当前 Node
- 是否避免无关的全流程发散
- 是否正确区分 PA、AS、A 和 SA
- 是否只为真正改变后续路径的情况新建 Flow

## 评估重点

首轮试用时，建议重点观察：

1. State 是否拆得过多或过少
2. 是否把普通异常错误地升级为 Flow
3. PA、AS、A、SA 是否使用准确
4. Action、Transition 与 Target 是否清楚分列
5. 已有编码是否保持稳定
6. 二维表是否方便直接用于设计稿管理
7. 必须出稿、建议出稿和仅需批注的分类是否实用

## 使用边界

- 该 Skill 用于结构化梳理和设计管理，不替代真实业务规则确认。
- `INFERRED` 和 `TO_CONFIRM` 内容需要产品、业务或合规人员进一步确认。
- 单页分析不会自动重构完整业务，除非用户明确要求。
- 弹窗、Toast、Sheet 默认作为 State 的 UI 表现，不会自动创建独立 Flow。
- 当操作导致后续 Node 集合明显变化时，才优先考虑 Trigger 新 Flow。

## 版本说明

当前版本为 V0.1 试用版，优先验证两个核心场景：

1. 完整原型或业务描述生成结构化流程表
2. 单个页面展开 State、Action、Trigger 和 UI 状态

建议根据实际试用结果逐步补充规则，避免一次性加入大量低频例外。
