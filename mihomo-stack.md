# Mihomo 代理栈与机场节点筛选方法论

> NAS 上用 [Mihomo](https://github.com/MetaCubeX/mihomo)（Clash 核心）跑全局/分流转发，并系统性筛选机场节点。
> 所有端口、节点名、订阅链接均为占位符。引用：[Mihomo](https://github.com/MetaCubeX/mihomo) · [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev)

## 1. 端口与服务

典型 Mihomo 配置（占位）：

```
mixed 端口:   <MIXED_PORT>   # HTTP+SOCKS 合一，客户端填这个
HTTP 端口:    <HTTP_PORT>
SOCKS 端口:   <SOCKS_PORT>
外部控制:     <CONTROLLER_PORT>   # 给 Clash Verge Rev / 面板用
```

- GUI 客户端 [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev) 连 `CONTROLLER_PORT` 做可视化管理。
- 改动配置**优先用配置里的 `prepend:` 段**写规则/组选择（最高优先级、不被订阅定时更新覆盖），目标指向**稳定组名**而非写死节点名（订阅改名也不失效）。

## 2. 节点筛选方法论（避免盲选）

不要手动挑节点，用「延迟实测 + 规则筛选」：

1. 用 `gstatic` 的 `204` 端点做延迟探测（比 ICMP 更贴近真实连通）。
2. **规则**：`gstatic204` 平均延迟 >150ms 或全程不可达 → 删；保留 ≤150ms 的节点。
3. 用脚本从订阅配置里**提取保留节点**、清理失效引用，并**删除变空的代理组**（否则启动报错）。
4. 筛选后做健康检查（连通性 + 目标站点可达性），确认零悬空引用再重启。

经验：

- 别只看「延迟低」，还要看「稳定性」——同一节点多次测，抖动大的也删。
- 保留 20 个左右高质量节点通常够用，太多反而拖累启动与切换。
- 分流规则：国内直连、国外走优选节点；可叠加 `script` 做按延迟自动选。

## 3. 常见坑

- **强杀 core 会断网**：改配置/切节点用控制 API 热更新（`PUT /proxies/<组名>` `{"name":"<节点>"}`），**绝不用 `taskkill` 强杀 core**——会造成整机代理断网空窗。
- API 走命名管道或本地端口，别暴露到公网。
- 订阅更新会覆盖手写节点，所以自定义规则放 `prepend` 段。

## 4. 参考

- Mihomo：https://github.com/MetaCubeX/mihomo
- Clash Verge Rev：https://github.com/clash-verge-rev/clash-verge-rev
