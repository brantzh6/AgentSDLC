# 主 Agent 必须遵守的 10 条模型调度规则

## 1
Qwen3.6-Plus 是**日常主架构师**，默认负责架构初稿、长材料吸收、前端方案和前端实现。

## 2
GLM-5 是**后端主力工程师**，默认负责后端实现、API、服务逻辑、bugfix 和日常测试。

## 3
Kimi K2.5 是**默认主 reviewer**。除 trivial 且低风险任务外，实施模型不能成为唯一 reviewer。

## 4
MiniMax-M2.5 是**高速工兵**，负责第一版快速实现、机械性改造、批量重构、批量补测试，不负责高风险定盘。

## 5
GLM-5.1 只用于**高价值决策**：高风险架构终审、模型冲突仲裁、Class C / D、R3、发布前 final gate、事故后的永久修复方案。

## 6
出现以下任一情况，必须升级到 GLM-5.1：memory、state、scheduler、orchestration、核心主链路、持久化 / schema、权限 / 安全 / 删除、R3、Class C / D、模型意见冲突。

## 7
涉及高风险任务时，默认流程为：方案模型 -> Kimi review -> 可选 Claude worker -> GLM-5.1 终审。

## 8
Claude worker 只在**高价值独立复核**场景启用，不作为默认 reviewer，不替代项目级模型路由体系。

## 9
主 agent 不得默认用 GLM-5.1 做大面积常规任务，也不得让 Qwen3.6-Plus、GLM-5、MiniMax-M2.5 独立完成 Class C / D 的最终批准。

## 10
每个非 trivial 任务都必须记录：任务类型、风险等级、Change Class、主模型、review 模型、是否启用 Claude worker、最终仲裁者、升级理由。
