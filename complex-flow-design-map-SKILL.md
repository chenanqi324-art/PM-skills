---
name: complex-flow-design-map
description: >
  用于复杂产品业务流程、原型流程和单页面的结构化梳理。
  自动识别完整流程分析、单页面展开、增量修改或流程审计场景，
  将业务组织为 Module → Flow → Node → State / Action → Transition → Target → UI，
  并输出统一编码、二维结构表、分支Flow、页面状态和设计稿清单。
---

# Complex Flow Design Map

## 1. Purpose

这个 Skill 用于处理流程较长、业务节点较多、页面状态复杂、存在大量分支和跨流程跳转的产品设计场景。

核心目标不是单纯“画流程图”，而是建立一套可长期维护的：

**业务结构 + 状态结构 + 页面索引体系**

最终所有业务与设计稿应尽可能映射到：

Module
→ Flow
→ Node
→ State / Action
→ Transition
→ Target
→ UI

适用场景包括：

- 完整业务原型流程梳理
- 多页面连续原型分析
- 用户口述复杂业务流程
- 单张页面的状态和分支补全
- 异常流程分析
- Child Flow / Branch Flow 识别
- 复杂设计稿编号与管理
- 已有业务流程增量修改
- 检查流程是否存在遗漏、重复或拆分不合理


# 2. Default Behavior

默认使用：

AUTO MODE

用户不需要手动选择分析模式。

收到输入后，先判断用户当前任务属于：

- FLOW_MAP
- PAGE_EXPAND
- UPDATE
- AUDIT
- MIXED

除非确实无法判断用户意图，否则不要要求用户先选择模式。


# 3. Intent Detection

## 3.1 FLOW_MAP

当用户提供以下内容时使用：

- 一张包含多个连续原型页面的长图
- 多张连续页面
- 完整流程图
- 一段端到端的业务口述
- 要求“完整流程”“整体梳理”“所有分支”
- 要求生成全流程结构化表格

目标：

先理解整个业务，再建立：

Module
→ Flow
→ Node
→ State / Action
→ Transition
→ Target
→ UI

重点识别：

- Happy Path
- Branch Flow
- 页面状态
- 异常状态
- 跨Flow跳转
- 跨端流程
- 回流流程
- 需要补充的设计稿


## 3.2 PAGE_EXPAND

当用户：

- 只提供一张页面截图
- 明确说“这个页面”
- 指定某个 Node
- 问“这个页面可能有哪些State / Action / Trigger”
- 希望补充某一个页面的异常和分支

使用 PAGE_EXPAND。

目标：

只深入当前 Node，不主动重构整个业务。

需要分析：

- 当前 Node 定位
- States
- Page Actions
- Action Slots
- State Actions
- System Actions
- Transition
- Target
- 是否 Trigger Child Flow
- 需要补充哪些 UI 稿


## 3.3 UPDATE

当用户：

- 在已有业务结构基础上新增规则
- 修改某个节点
- 新增一个 State / Action / Flow
- 明确说保持已有编码
- 说“在原来的基础上修改”

使用 UPDATE。

要求：

- 保持已有 Module / Flow / Node 编码
- 不重新编号无关内容
- 只更新受影响区域
- 明确新增、修改、删除内容
- 说明受影响的 UI


## 3.4 AUDIT

当用户要求：

- 检查流程是否完整
- 检查有没有遗漏状态
- 检查编号是否合理
- 检查哪些 State 应升级成 Flow
- 查找重复页面 / 重复流程
- 判断流程结构是否过度复杂

使用 AUDIT。


## 3.5 MIXED

如果用户提供完整流程，同时要求重点展开某一个页面：

整体理解使用 FLOW_MAP，
目标页面使用 PAGE_EXPAND。

不要为了展开单页而重新梳理所有页面。


# 4. Core Data Model

整套结构遵循：

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

重要：

State 和 Action 是 Node 下的两个并列维度。

不要默认使用：

State
└── Action

这种严格父子结构。


# 5. Core Definitions

## 5.1 Module

Module 表示大的业务阶段。

示例：

A 认证承接
B 实名认证
C 合格投资者确认
D 风险测评
E 开户绑卡

编码：

A
B
C
D
...

Module 只表达大的业务阶段。

不要在 Module 层塞入页面异常。


## 5.2 Flow

Flow 表示一条连续、具有明确业务目的的路径。

示例：

B1 弱实名主流程
B2 OCR强实名流程
B3 手动强实名流程

编码：

B1
B2
B3

一个 Module 可以包含多个 Flow。

如果某个操作使后续业务节点集合发生明显变化，应考虑 Trigger 新 Flow。


## 5.3 Node

Node 表示 Flow 中的核心业务步骤。

通常对应：

- 一个核心页面
- 一个明确业务动作阶段
- 一个系统判断节点

示例：

B2.1 身份证上传
B2.2 OCR识别
B2.3 身份信息确认

编码：

[Flow].[Node序号]

例如：

B2.1
E1.3

不要把每一个输入框、按钮、Toast 都拆成 Node。


# 6. State

State 表示：

当前 Node 在不同业务条件下的页面 / 业务状态。

编码：

[Node]-Sxx

例如：

B2.1-S01 默认态
B2.1-S02 已上传一面
B2.1-S03 两面上传完成
B2.1-S20 上传失败

State 判断原则：

如果核心任务没有变化，只是：

- 信息不同
- 页面表现不同
- 按钮状态不同
- 加载 / 等待
- 输入错误
- 提交异常
- 审核状态不同

优先视为同一 Node 下的 State。

不要因为 UI 长得不一样就自动创建新 Flow。


# 7. Action Types

Action 分为四类。


## 7.1 PA — Page Action

页面固定操作入口。

绑定 Node，不绑定某个特定 State。

编码：

[Node]-PAxx

示例：

B1.1-PA01 使用OCR填写
B1.1-PA02 港澳台及海外人员填写
E1.2-PA01 预留手机号无法使用

适合：

- 页面长期存在的入口
- 多个 State 下均可使用
- 固定功能操作

PA 可以：

- Stay
- Show
- Continue
- Trigger
- Return

PA 点击后可以直接 Trigger 新 Flow。


## 7.2 AS — Action Slot

页面固定的操作位置，但：

- 文案
- 是否可用
- Loading状态
- 点击行为
- Target

会根据 State 改变。

编码：

[Node]-ASxx

示例：

A1.1-AS01 底部主CTA

可能表现：

S01 → 开始认证
S02 → 继续认证
S03 → 继续测评
S05 → 重新完善

同一个按钮位置，不要因为文案变化就重新创建多个 Action 编码。


## 7.3 A — State Action

只在某个特定 State 下额外出现的操作。

编码：

[Node]-[State]-Axx

例如：

B2.2-S20-A01 前往手动填写
E1.2-S10-A02 继续绑卡

State Action 通常用于：

- 重试
- 重新完善
- 手动处理
- 特殊异常操作
- 特定状态下新增按钮


## 7.4 SA — System Action

系统自动执行的动作。

编码：

[Node]-[State]-SAxx

例如：

B2.2-S02-SA01
OCR识别成功后自动进入身份信息确认

适合：

- 自动跳转
- 自动识别
- 自动更新
- 后台状态变化
- 自动路由


# 8. Transition

Action 与 Target 必须拆开描述。

不要把：

“点击OCR进入强实名流程”

只写成一句自然语言。

应该拆成：

Action：
使用OCR填写

Transition：
Trigger

Target：
B2 OCR强实名流程


Transition 尽量只使用以下固定类型：

### Stay

仍停留当前 Node。

例：

填写姓名
→ Stay B1.1


### Continue

正常进入下一个 Node / Flow。

例：

确认信息
→ Continue C1


### Submit

发起业务提交或服务请求。

例：

提交二要素
→ Submit B1.2


### Trigger

触发新的 Branch / Child Flow。

例：

使用OCR
→ Trigger B2


### Route

根据条件动态选择不同 Target。

例：

继续认证
→ Route 剩余未完成项


### Resume

恢复已有流程进度。

例：

继续风险测评
→ Resume D2 当前题目


### Retry

重新执行当前 Node 或 Action。

例：

OCR失败
→ Retry B2.1


### Return

返回来源页面、父流程或原业务场景。


### Wait

等待系统状态变化。


### Show

展示：

- Modal
- Sheet
- Toast
- Help
- Inline提示

但不离开当前业务 Node。


### End

当前业务路径结束。


# 9. State vs Flow Decision Rule

判断一个情况应该是 State 还是新 Flow：

第一问：

只是当前页面内容、提示、按钮状态发生变化吗？

如果是：

→ State

第二问：

用户处理后仍然回当前 Node / 原业务路径吗？

如果是：

→ State + Action

第三问：

这个操作后，后续 Node 集合发生变化了吗？

如果是：

→ Trigger 新 Flow


核心口诀：

页面表现变化
= State

后续业务路径变化
= Flow


示例：

验证码错误
→ Retry当前验证码页
= State

预留手机号不可用
→ 不走验证码
→ 不开快捷支付
→ 改走仅绑卡流程
= Trigger新Flow


# 10. Modal / Sheet / Toast Rule

弹窗默认不是 Flow。

例如：

OCR失败
→ 出现失败提示弹窗
→ 点击前往手动填写
→ Trigger B3

结构应该是：

B2.2-S20 OCR失败
↓
UI-B2.2-S20-M01
↓
B2.2-S20-A01 前往填写
↓
Trigger B3

只有当弹窗内部本身存在多个连续业务步骤时，
才考虑把它升级成独立 Flow。


# 11. UI Coding

UI 编码：

UI-[Node]-[State]-[Type][No]

推荐 UI Type：

P = Page
M = Modal
S = Sheet
T = Toast
I = Inline

示例：

UI-B2.1-S01-P01
默认身份证上传页

UI-B2.2-S20-M01
OCR失败弹窗

UI-E1.2-S10-S01
手机号不可用半屏

如果只有一个完整页面，也可简化成：

UI-B2.1-S01

不要为了编码而制造无意义复杂度。


# 12. Confidence Level

在分析用户没有明确给出的业务状态时，必须区分：

CONFIRMED

用户明确描述、流程图明确存在或页面明确表现。


INFERRED

根据当前业务、交互和常见异常合理推断。


TO_CONFIRM

会显著影响业务路径，但当前信息不足，需要业务确认。


不要把 INFERRED 内容直接描述成既定需求。


# 13. FLOW_MAP Workflow

当进入 FLOW_MAP 时：

## Step 1

识别完整 Happy Path。

优先回答：

用户从哪里进入？
最终去哪里？
中间核心业务阶段是什么？


## Step 2

拆 Module。


## Step 3

拆每个 Module 内的 Flow。


## Step 4

拆 Flow 内 Node。


## Step 5

判断图片中重复页面属于：

- 同一个 Node 的不同 State
还是
- 新 Node
还是
- 新 Flow


## Step 6

梳理每个 Node 的：

- State
- PA
- AS
- State Action
- System Action


## Step 7

识别：

Transition
+
Target


## Step 8

识别可能 Trigger 的 Child Flow。


## Step 9

补充合理遗漏：

- 默认态
- 填写中
- 可提交
- Loading
- 提交失败
- 业务校验失败
- 审核中
- 审核失败
- 中断恢复
- 跨端能力

但必须使用 CONFIRMED / INFERRED / TO_CONFIRM 标记。


## Step 10

映射需要真正产出的 UI。


# 14. PAGE_EXPAND Workflow

当进入 PAGE_EXPAND：

## Step 1

判断当前页面所属：

Module
Flow
Node

如果已有编号：

沿用。

绝对不要擅自重新编号。

如果没有编号：

提供建议编号，但标记为：

Suggested Code


## Step 2

先识别页面固定结构：

- 页面任务是什么
- 哪些入口一直存在
- 底部主操作位是否固定
- 页面是否包含弹窗 / 半屏


## Step 3

识别 States。

至少考虑：

- 默认态
- 填写中
- 可提交
- Loading / Processing
- 页面级错误
- 业务校验错误

但不要机械生成所有状态。


## Step 4

识别 PA。


## Step 5

识别 AS。


## Step 6

识别特殊 State Action。


## Step 7

识别 System Action。


## Step 8

为每个 Action 定义：

Transition
Target


## Step 9

判断是否需要 Trigger Child Flow。


## Step 10

输出：

必须出稿
建议出稿
仅需批注


# 15. UPDATE Workflow

当用户修改已有逻辑：

1. 找到受影响 Module / Flow / Node
2. 保持所有无关编码
3. 优先追加新 State / Action
4. 如果必须新增 Flow，使用下一个未占用 Flow 编码
5. 明确哪些 UI 需要新增
6. 明确哪些旧 UI 需要修改
7. 不重新输出无关全流程，除非用户要求


# 16. AUDIT Workflow

检查：

- 是否把 State 错拆成 Flow
- 是否把不同 Flow 错塞成 State
- PA 是否重复绑定每个 State
- AS 是否被错误拆成多个按钮
- State Action 是否被误写成 PA
- 弹窗是否被过度拆成 Flow
- 是否存在重复 UI
- 是否有不可达 Node
- 是否缺少 Return / Resume
- 是否缺少异常状态
- 是否缺少跨端分支
- 编码是否冲突
- Target 是否不存在
- 是否有未闭环 Branch


# 17. Required Output — FLOW_MAP

FLOW_MAP 默认输出以下内容。


## Part 1 — Business Tree

优先使用简单层级树：

Module
├─ Flow
│  ├─ Node
│  └─ Node
└─ Flow


## Part 2 — Structured Master Table

固定表头：

| 模块编码 | 模块说明 | Flow编码 | Flow说明 | Node编码 | Node说明 | State编码 | State说明 | State判断条件 | Action类型 | Action编码 | Action说明 | Action可用条件 | Transition | Target编码 | Target说明 | UI编码 | UI说明 |

编码和说明必须分列。

不要把：

B2.1-S20 OCR失败

全部塞进一个字段。


## Part 3 — Trigger / Branch List

单独总结：

来源
→ Action
→ Transition
→ Target

例如：

B1.1-PA01
→ Trigger
→ B2


## Part 4 — Missing / Potential States

分为：

CONFIRMED
INFERRED
TO_CONFIRM


## Part 5 — UI Checklist

分为：

必须出稿
建议出稿
仅需交互批注


# 18. Required Output — PAGE_EXPAND

PAGE_EXPAND 默认输出：


## Part 1 — Node Position

说明当前页面：

Module
Flow
Node
核心页面任务


## Part 2 — Node Structure

例如：

B2.1 身份证上传
├─ States
├─ PA
├─ AS
├─ State Action
└─ SA


## Part 3 — Local Structured Table

使用相同主表表头：

| 模块编码 | 模块说明 | Flow编码 | Flow说明 | Node编码 | Node说明 | State编码 | State说明 | State判断条件 | Action类型 | Action编码 | Action说明 | Action可用条件 | Transition | Target编码 | Target说明 | UI编码 | UI说明 |


## Part 4 — Branch Analysis

明确指出：

哪些只是 State
哪些 Stay
哪些 Retry
哪些 Continue
哪些 Trigger 新 Flow


## Part 5 — UI Checklist

必须出稿
建议出稿
仅批注


# 19. Design Board Recommendation

当用户同时问设计稿应该如何排版时：

不要建议把所有 UI 无限横向铺开。

推荐每个 Module / Flow：

01 Flow Map

02 Node Section

03 States / Variants

04 UI Frames

05 Actions / Transition

06 Notes


一个 Node 内推荐：

Node Title

[主UI]

[State Variant 1]
[State Variant 2]
[State Variant 3]

右侧统一标：

State
Action
Transition
Target


Trigger 新 Flow 时：

不要拉超长箭头跨越整张画布。

使用：

Trigger → B3

这类 Flow Reference 标签。

然后在 B3 Section 中单独展开。


# 20. Important Rules

必须遵守：

1. 不要因为每个异常都建立新 Flow。
2. 不要默认把弹窗当新 Flow。
3. 不要把所有 Action 都强绑定到 State。
4. 页面固定入口优先使用 PA。
5. 固定位置但随状态变化的主按钮优先使用 AS。
6. 特定 State 才出现的操作使用 State Action。
7. 系统自动跳转使用 SA。
8. State 与 Action 是 Node 下并列维度。
9. 后续 Node 集合变化时才优先考虑 Trigger Flow。
10. 不要过早抽象 Global State。
11. 先保证局部业务准确，再在流程全部稳定后总结 Common State / Common Component。
12. 不要因为原型里同一个页面复制多次，就默认它们是不同 Node。
13. 不要为了“完整”把相同后续流程重复画多遍。
14. 如果 Child Flow 最终回主 Flow，用 Return / Continue 引用目标 Node。
15. 已有编码必须优先继承，不得随意重编。


# 21. How to Handle Images

如果用户提供完整原型长图：

不要机械按图片从左到右编号。

先识别：

- 页面标题
- 分组
- 箭头
- 重复页面
- 弹窗
- 状态变体
- 上下游关系

然后再重建业务结构。


如果用户提供单页：

只从当前页面能确定的内容出发。

结合用户文字说明补充流程。

如果上下游 Target 无法确认：

可以完成本页 State / Action 分析，
Target 标记 TO_CONFIRM。

不要因为 Target 不确定就停止整个分析。


# 22. Response Style

默认：

- 先给结论，再给表格
- 优先具体业务语言
- 不讲过多抽象理论，除非用户询问方法
- 编码必须稳定
- 对用户已确定规则，不重复质疑
- 不要一次性发散大量低概率异常
- 优先覆盖高频、高影响、会改变流程或需要独立UI的状态

如果用户当前只分析一个页面：

不要顺便把整个产品重新梳理一遍。
