# Server酱微信通知自动化

> 用 [Server酱（方糖）](https://sct.ftqq.com) 把 AI 任务进度 / 异常 / 每日报告推到微信，人在外面也能收到。
> 所有 `SENDKEY` 均为占位符。**SENDKEY 等同密码，切勿写进会公开的文档/仓库。**
> 引用：[Server酱 / 方糖](https://sct.ftqq.com)

## 1. 原理

```
任意脚本 / AI 任务完成
        │  HTTP GET/POST
        ▼
  Server酱 API: https://sctapi.ftqq.com/<SENDKEY>.send
        │
        ▼
   你的微信（关注方糖公众号并绑定）
```

## 2. 最小可用

```
GET https://sctapi.ftqq.com/<SENDKEY>.send?title=任务完成&desp=详情
# 或 POST application/x-www-form-urlencoded: title=...&desp=...
```

- `title`：标题（短）；`desp`：正文，支持简单 Markdown。
- 在任意脚本末尾调一下即可（bash / python / node 都行）。

## 3. 典型场景（与本文档其他篇联动）

- **OpenClaw 任务完成通知**：见 [openclaw-fnOS.md](openclaw-fnOS.md) 的「任务完成 → 微信通知」钩子。
- **每日健康报告**：cron 触发 → 生成报告 → 调 Server酱推微信。
- **异常告警**：长任务（模型推理 / 测速 / 传输）超时或失败，自动推微信。

## 4. 踩坑

1. **频率限制**：免费/普通档有每日条数上限，别在循环里疯狂发；批量事件合并成一条。
2. **desp 太长被截断**：正文控制在合理长度，关键信息放 title。
3. **Markdown 别乱用**：只支持基础语法，复杂表格会渲染异常。
4. **SENDKEY 保密**：写进脚本时走环境变量，别硬编码进会提交的代码。
5. **绑定确认**：先关注「方糖」公众号并绑定 SENDKEY，否则收不到。

## 5. 参考

- Server酱（方糖）：https://sct.ftqq.com
