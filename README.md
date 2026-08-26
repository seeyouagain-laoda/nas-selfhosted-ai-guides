# NAS 自托管 AI 工具栈实战教程合集

> 在飞牛 fnOS / 群晖等 NAS 上自托管 AI 工具栈的中文实战笔记：OpenClaw、Mihomo 代理、Tailscale、Gemini / pplx 反代、Server酱微信通知、MiniMax H3 视频生成。
> 所有内网 IP、API Key、订阅节点、内部 ID 等敏感信息均已脱敏为占位符；架构、命令与避坑经验保留。
> 文内引用各上游开源项目（见文末 [参考与引用](#参考与引用)）。

## 目录

- [OpenClaw 在飞牛 fnOS 上的完整实践](openclaw-fnOS.md) — 安装、每日健康报告推送、任务完成微信通知、浏览器 CDP 接入
- [反向代理实战：Gemini 与 Perplexity(pplx)](reverse-proxies.md) — 自建反代解决区域限制 / 统一端点
- [Mihomo 代理栈与机场节点筛选方法论](mihomo-stack.md) — 代理系统、节点筛选、性能与稳定性
- [Tailscale 异地组网](tailscale-mesh.md) — 家庭多设备 / NAS 组网
- [Server酱微信通知自动化](wechat-notify.md) — 任务完成 / 异常推微信
- [MiniMax H3 视频生成工具](minimax-h3.md) — 本地视频生成工作流

## 适用场景

- 想把自己家的 NAS 变成 AI 中枢：跑 Agent、统一代理、跨端记忆、自动通知
- 被各种「区域限制 / 订阅额度 / 端点不统一」折磨，想自建反代
- 想用微信接收 AI 任务进度

## 免责声明

- 本仓库仅记录个人部署经验与通用方法论，**不含任何供应商密钥、订阅或凭据**。
- 请遵守各上游服务的使用条款；反代类方案仅供自用学习。

## 参考与引用

- **TencentDB Agent Memory**（腾讯开源多 Agent 记忆库）：https://github.com/TencentCloud/TencentDB-Agent-Memory
- **Mihomo**（Clash 核心）：https://github.com/MetaCubeX/mihomo
- **Clash Verge Rev**（GUI 客户端）：https://github.com/clash-verge-rev/clash-verge-rev
- **Tailscale**：https://github.com/tailscale/tailscale
- **MiniMax AI**：https://www.minimax.io （开源组织：https://github.com/MiniMax-AI）
- **Server酱 / 方糖**（微信通知）：https://sct.ftqq.com
- **飞牛 fnOS**：https://www.fnnas.com
- **Google Gemini**：https://ai.google.dev
- **Perplexity API**：https://docs.perplexity.ai
- **OpenClaw** — 开源自托管 AI Agent 框架（具体仓库以官方发布为准）
