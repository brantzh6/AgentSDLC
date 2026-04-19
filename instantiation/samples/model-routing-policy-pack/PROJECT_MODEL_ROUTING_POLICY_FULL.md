# 项目级模型路由与 Worker 编排规则

## 1. 规则定位

本规则属于**项目级运行策略**，不属于通用 SDLC 主合同。

它用于指导主 agent（如 OpenClaw、Hermes）在具体项目中如何：

- 为不同任务选择主模型
- 何时启用独立 reviewer
- 何时启用 Claude worker
- 何时升级到更高强度模型
- 如何在架构、编码、review、测试、终审之间做模型分工

主 agent 必须把本规则理解为：

- 项目级模型岗位分工表
- 任务路由策略
- 升级与仲裁规则
- Claude worker 编排规则

---

## 2. 总体原则

### 2.1 稀缺模型只用于高价值决策
高成本、低额度、稀缺模型不得作为常规铺量模型。
它们只应用于：

- 高风险架构定盘
- 终审
- 冲突仲裁
- 高风险验证
- 事故后永久修复方案

### 2.2 大额度模型负责日常主线
常规架构、常规编码、常规测试、常规重构，应优先由大额度模型承担。

### 2.3 Review 与 Implementation 分离
默认情况下，实施模型不能成为唯一 reviewer。

### 2.4 高风险任务必须升级
涉及 memory、state、scheduler、orchestration、核心后端主链路、持久化、权限、安全、删除逻辑、不可逆操作时，必须升级到更高强度模型或增加独立 review。

### 2.5 最强模型不是默认模型
最强模型用于定盘，不用于常规吞吐。

---

## 3. 模型岗位定义

## 3.1 Qwen3.6-Plus
默认定位：

- 日常主架构师
- 前端主力
- 大材料吸收器
- 长上下文方案整理者

适合：

- 日常方案设计
- OpenClaw / Hermes / workflow 架构初稿
- 长文档、长日志、长需求整理
- 前端页面、组件、GUI、交互相关实现
- 图文混合材料驱动的任务
- 大量历史上下文吸收后给出结构化方案

默认不独立承担：

- Class C / D 的最终仲裁
- 高风险架构最终拍板

## 3.2 GLM-5.1
默认定位：

- 高风险架构终审
- 冲突仲裁者
- 关键方案定盘者
- 高风险测试策略设计者
- 发布前 final gate 审核者

适合：

- memory / state / scheduler / orchestration
- 核心后端主链路架构
- R3 任务
- Class C / D 任务
- 多模型意见冲突
- 事故后的永久修复方案
- 发布前高风险验证总设计

默认不承担：

- 常规编码
- 常规 review
- 大面积测试生成
- 批量重构
- 日常铺量实现

## 3.3 GLM-5
默认定位：

- 后端主力工程师
- 日常测试主力
- 工程执行主力

适合：

- 后端服务实现
- API / 业务逻辑 / 普通重构
- bugfix
- 单测 / 集成测试 / 回归测试
- 实现闭环
- 常规工程化改动

默认不独立承担：

- 高风险架构终审
- 唯一 reviewer
- R3 / Class C / D 的最终拍板

## 3.4 Kimi K2.5
默认定位：

- 主 reviewer
- 反方审稿人
- 边界条件与反例发现者
- 测试盲点检查者

适合：

- 方案 review
- 代码 review
- 测试 review
- 找边界条件
- 找反例
- 找偏题
- 检查是否只覆盖 happy path
- 检查是否过度设计

默认不承担：

- 大面积主实现
- 高频铺量编码
- 高速批量重构主力

## 3.5 MiniMax-M2.5
默认定位：

- 高速代码工兵
- 批量重构者
- 批量补测试者
- 第一版快速实现者

适合：

- 第一版快速实现
- 机械性改造
- 批量补单测
- 样板代码
- 大规模重构
- 高频低复杂度任务
- 批量补 mock / fixture

默认不独立承担：

- 主架构设计
- 高风险终审
- 独立方案定盘

## 3.6 Claude Worker
默认定位：

- 可选独立第二视角 reviewer
- 与主模型体系相对独立的外部审稿人

适合：

- 高风险任务的独立第二视角 review
- 架构争议的额外审稿
- 主 agent 对当前结论信心不足时的外部复核
- 事故后关键修复的附加独立审稿
- Class C / D 的高价值独立复核

默认不承担：

- 常规铺量编码
- 高频低风险任务
- 替代整个项目级模型路由体系

---

## 4. 默认任务分配规则

## 4.1 架构任务

### 日常架构设计
默认模型：**Qwen3.6-Plus**

适用：
- 日常方案
- 系统拆分
- 长材料吸收后的方案初稿
- 工作流 / agent 系统的结构梳理

默认 reviewer：**Kimi K2.5**

### 高风险架构任务
默认流程：
1. Qwen3.6-Plus 出初稿
2. Kimi K2.5 做 review
3. Claude worker（可选）做独立第二视角 review
4. GLM-5.1 做最终终审 / 仲裁

触发条件：
- memory
- state
- scheduler
- orchestration
- 核心后端主链路
- 持久化 / schema
- 权限 / 删除 / 安全
- 不同模型结论冲突
- R3 / Class C / D

### 前端架构 / 页面信息架构
默认模型：**Qwen3.6-Plus**

---

## 4.2 编码任务

### 后端编码
默认模型：**GLM-5**

适用：
- API
- 服务逻辑
- 普通重构
- bugfix
- 常规集成

默认 reviewer：**Kimi K2.5**

### 前端编码
默认模型：**Qwen3.6-Plus**

适用：
- 页面实现
- 组件拆分
- 图文驱动的页面还原
- 交互逻辑
- GUI 相关任务

默认 reviewer：**Kimi K2.5**

### 高速铺量编码
默认模型：**MiniMax-M2.5**

适用：
- 第一版快速实现
- 批量样板代码
- 机械性重构
- 批量补测试

默认 reviewer：**Kimi K2.5**

### 高风险编码
默认流程：
1. GLM-5 主实现
2. Kimi K2.5 review
3. 必要时 Claude worker 独立复核
4. GLM-5.1 终审

触发条件：
- 核心链路
- state / memory / scheduler
- 数据层 / 持久化层
- 高并发
- 生产事故修复
- R3 / Class C / D

---

## 4.3 Review 任务

### 默认主 review
默认模型：**Kimi K2.5**

职责：
- 找漏洞
- 找边界条件
- 找偏题
- 检查逻辑跳跃
- 检查是否过度设计
- 检查方案和实现是否一致

### 长材料一致性 review
默认模型：**Qwen3.6-Plus**

适用：
- 需要核对超长需求、会议纪要、设计稿、长日志、图文混合材料
- 检查实现是否遗漏超长上下文要求

### 高风险终审 review
默认模型：**GLM-5.1**

适用：
- R3
- Class C / D
- 发布前 final gate
- review 结论冲突

### Claude worker review
启用条件：
- 高风险任务需要独立第二视角
- 主 agent 对当前结论信心不足
- 架构方案重大争议
- 事故后关键修复需要额外复核

Claude worker 不作为默认 reviewer。

---

## 4.4 测试任务

### 日常测试实现
默认模型：**GLM-5**

适用：
- 单测
- 集成测试
- 回归测试
- bug repro
- smoke test

### 批量补测试
默认模型：**MiniMax-M2.5**

适用：
- 批量生成测试
- 批量补 mock / fixture
- 批量补回归测试

### 前端 / GUI / 图文测试
默认模型：**Qwen3.6-Plus**

适用：
- 页面行为测试设计
- GUI 场景测试
- 基于截图 / 设计稿 / PRD 的测试任务

### 测试 review / 找漏测点
默认模型：**Kimi K2.5**

适用：
- 检查是否只测 happy path
- 检查异常场景
- 检查边界条件
- 检查对抗性 case

### 高风险测试策略
默认模型：**GLM-5.1**

适用：
- 发布前高风险验证方案
- 事故后防复发测试设计
- memory / scheduler / stateful flow 的验证总策略

---

## 5. 升级与仲裁规则

## 5.1 必须升级到 GLM-5.1 的情况
出现以下任一情况，主 agent 必须升级到 GLM-5.1：

- 涉及 memory / state / scheduler / orchestration
- 涉及核心主链路架构
- 涉及持久化 / schema / 关键数据路径
- 涉及权限、安全、删除逻辑
- 属于 R3
- 属于 Class C / D
- 不同模型结论冲突
- 发布前 final gate 为高风险
- 事故后的永久修复方案定盘

## 5.2 必须启用 Kimi K2.5 review 的情况
出现以下任一情况，主 agent 必须启用 Kimi review：

- 实施模型完成后需要独立 review
- 方案存在明显复杂度
- 测试需要找盲点
- 需求边界不够清晰
- 需要检查是否过度设计
- 需要挑战当前实现思路

除 trivial 且低风险任务外，Kimi K2.5 默认作为独立 reviewer。

## 5.3 可选启用 Claude worker 的情况
主 agent 可在以下情况启用 Claude worker：

- Class C / D 需要额外独立审稿
- 对主路线方案置信度不足
- 架构结论存在争议
- 重大事故后的关键修复需要第二外部视角
- 需要与内部模型体系保持相对独立的审稿意见

主 agent 不得把 Claude worker 当作默认 reviewer。

---

## 6. 默认工作流

## 6.1 日常功能开发
1. Qwen3.6-Plus 读材料并出方案初稿  
2. GLM-5 负责后端实现，或 Qwen3.6-Plus 负责前端实现  
3. MiniMax-M2.5 承担批量补码 / 重构 / 补测试  
4. Kimi K2.5 做主 review  
5. 仅在高风险时升级到 GLM-5.1

## 6.2 高风险架构任务
1. Qwen3.6-Plus 出方案初稿  
2. Kimi K2.5 review  
3. Claude worker（可选）做独立第二视角 review  
4. GLM-5.1 终审定盘  
5. GLM-5 负责实现  
6. Kimi K2.5 再做实现 review  
7. GLM-5.1 负责 final gate

## 6.3 事故修复
1. GLM-5 或 MiniMax-M2.5 快速实施临时修复  
2. Kimi K2.5 检查是否只修表面  
3. Qwen3.6-Plus 整理事故上下文与长期方案  
4. GLM-5.1 确认永久修复方案与防复发策略  
5. GLM-5 负责永久修复实现  
6. Kimi K2.5 review  
7. GLM-5.1 审核高风险发布前验证

---

## 7. 禁止规则

主 agent 不得：

- 默认用 GLM-5.1 做大面积常规任务
- 让实施模型成为唯一 reviewer
- 用 MiniMax-M2.5 独立决定高风险架构
- 用 Qwen3.6-Plus 独立完成 Class C / D 的终审
- 用 Claude worker 替代整个项目级模型路由体系
- 在高风险任务中跳过独立 review
- 在模型结论冲突时不做升级仲裁

---

## 8. 输出与记录要求

每次主 agent 选择模型时，必须至少记录：

- 当前任务类型
- 当前风险等级
- 当前 Change Class（如项目已启用）
- 选择的主模型
- 是否启用 reviewer
- 是否启用 Claude worker
- 是否升级到 GLM-5.1
- 升级理由

推荐格式：

```text
Task Type: backend / frontend / architecture / review / test / incident
Risk Level: R1 / R2 / R3
Change Class: A / B / C / D
Primary Model: ...
Review Model: ...
Claude Worker: enabled / disabled
Final Arbiter: ...
Reason: ...
```

---

## 9. 一句话总规则

**Qwen3.6-Plus 负责日常架构与前端，GLM-5 负责后端与日常测试，Kimi K2.5 负责主 review，MiniMax-M2.5 负责高速铺量，GLM-5.1 只负责高风险终审与关键仲裁；Claude worker 只在高价值独立复核场景启用。**
