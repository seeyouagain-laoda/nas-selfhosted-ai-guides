# 反向代理实战：Gemini 与 Perplexity(pplx)

> 自建反代解决两类痛点：① 区域限制 / 端点不稳；② 多供应商端点不统一。
> 本文以 Gemini 反代、Perplexity(pplx) 反代为例，给出架构、关键坑与脱敏配置骨架。
> 所有域名、证书路径、Key 均为占位符（`<YOUR_...>`）。反代仅供自用学习，请遵守各服务条款。
> 引用：[Google Gemini](https://ai.google.dev) · [Perplexity API](https://docs.perplexity.ai) · [Mihomo](https://github.com/MetaCubeX/mihomo)

## 1. 通用架构

```
客户端 / 其他 AI 工具
        │  HTTPS（自有域名 + Let's Encrypt）
        ▼
  反向代理 (nginx / 网关)
        │  HTTP 转发（内网）
        ▼
   后端服务容器 (Gemini 反代 / pplx-proxy)
        │
        ▼
   上游供应商 API
```

要点：

- **前端用自有域名 + Let's Encrypt**，不要用自签证书（客户端不信任会连不上）。
- 反代与后端同在内网，转发走 HTTP 即可。
- 证书续期交给 Let's Encrypt 自动 renew，别手写 cron 硬刷。

## 2. Gemini 反代

典型问题（三层根因，按此顺序排查）：

1. **TUN 劫持**：NAS 上的 TUN 设备被其他 VPN 占用，导致出站走错路 → 先停冲突 VPN / 调整路由。
2. **Cookie 仍是 Guest**：反代没把登录态 cookie 正确透传，后端以 Guest 身份被限流 → 检查 cookie 域名/透传头。
3. **占位符补丁**：上游返回体里的占位符需后端替换成真实地址，漏补会 400/空响应 → 打上占位符替换补丁。

验证：

- 用 `curl` 直连反代端点，确认返回 200 且 body 正常。
- 生图类接口要单独验证（生图走不同路径，容易"对话通、生图不通"）。

## 3. Perplexity(pplx) 反代 — pplx-proxy

`pplx-proxy` 把 Perplexity API 反代成一个本地 OpenAI 兼容端点，并做模型别名管理。

- **容器**：`pplx-proxy`（主），历史上曾挂 `flaresolverr` 解 Cloudflare 挑战。
- ⚠️ **状态覆盖（2026-08 起）**：`flaresolverr` 已**移除/停用**——它会和反代 `/health` 形成死循环、疯狂烧 CPU。当前 EMPTY/挑战类排查直接走 `/health` 配额 + slug 核验，勿重新启用该容器。
- **模型别名**：维护一份别名表（如 `sonar` / `gpt-5.5-terra` 等），健康检查应打 `/v1/models`（而非硬编码），否则订阅更新会覆盖配置。
- **健康检查**：`GET /health` 返回配额/状态；`GET /v1/models` 列出可用模型。

客户端接入（脱敏）：

```
Base URL: http://<NAS-LAN-IP>:<PPLX_PORT>/v1
API Key:  <YOUR_PPLX_KEY>
模型:     <ALIAS_NAME>
```

## 4. 常见翻车点

- 双 nginx 冲突：fnOS 自带 nginx 与反代 nginx 抢 80/443，需错开或反向代理串接。
- fnOS nginx 崩溃：改配置后偶发崩溃，需重启 fnOS 网络栈。
- 证书不信任：换 Let's Encrypt 后客户端仍不信任 → 清客户端证书缓存 / 确认链完整。
- 手机流量连不上：走公网域名时若被运营商拦，兜底用 Tailscale（见 [tailscale-mesh.md](tailscale-mesh.md)）。

## 5. 参考

- Google Gemini：https://ai.google.dev
- Perplexity API 文档：https://docs.perplexity.ai
- Mihomo（反代后端常跑在 NAS 代理同一网络）：https://github.com/MetaCubeX/mihomo
