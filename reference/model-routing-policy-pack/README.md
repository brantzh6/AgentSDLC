# 项目级模型路由与 Worker 编排规则包

本包属于**项目级运行策略**，不是通用 SDLC 规则。

适用对象：
- 主 agent（如 OpenClaw、Hermes）
- 项目级 runtime policy / model routing policy
- Claude worker 启用策略
- Project Profile / Runtime Profile / Team Playbook

本包包含：

1. `PROJECT_MODEL_ROUTING_POLICY_FULL.md`
   - 完整版项目级模型路由与 worker 编排规则
   - 适合放入 Project Profile / Runtime Profile

2. `PROJECT_MODEL_ROUTING_POLICY_SHORT.md`
   - 更适合直接给主 agent 读取的短规则体
   - 适合放入 `agents.md`、主 agent system prompt、runtime routing policy

3. `PROJECT_MODEL_ROUTING_RULES_10.md`
   - 10 条极简调度规则
   - 适合放在主 agent 的最上层硬规则区

## 推荐放置位置

- `docs/project/PROJECT_MODEL_ROUTING_POLICY_FULL.md`
- `.agent/runtime/PROJECT_MODEL_ROUTING_POLICY_SHORT.md`
- `.agent/runtime/PROJECT_MODEL_ROUTING_RULES_10.md`

## 使用原则

- 这是**项目级规则**，不进入通用 SDLC 主合同。
- 主 agent 必须基于任务类型、风险等级、变更类型和当前阶段来路由模型。
- 稀缺模型（如 GLM-5.1）只用于高价值终审、仲裁和高风险验证。
- Claude worker 只在高价值独立复核场景启用，不作为默认 reviewer。

## 当前模型岗位分工主旨

- Qwen3.6-Plus：日常架构 + 前端 + 大材料吸收
- GLM-5：后端 + 日常测试
- Kimi K2.5：主 review
- MiniMax-M2.5：高速铺量 / 重构 / 批量补测试
- GLM-5.1：高风险终审 / 仲裁 / 最终 gate
- Claude worker：可选独立第二视角
