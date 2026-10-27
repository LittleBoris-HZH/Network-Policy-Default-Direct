# Network-Policy-Default-Direct

WEPC 的人工域名策略库，适用于 **Mihomo / Clash Verge Rev** 的默认直连（Default Direct）架构。

## 核心策略

- 未匹配规则的流量最终由客户端 `MATCH,DIRECT` 直连。
- 需要海外出口的已知域名由 [Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules) 的 `gfw.txt` 和本仓库的 `custom-proxy.yaml` 分流至 WEPC。
- 广告及跟踪域名由 Loyalsoldier `reject.txt` 拦截；额外的人工阻断域名放在 `custom-reject.yaml`。
- `custom-direct.yaml` 只保留需要明确纠正代理误判的直连例外。日常国内网站无须逐个加入直连名单。
- Jump Desktop 官方域名、本地网络 CIDR 与最后的 `MATCH,DIRECT` 规则保留在各设备的本地 YAML；不依赖本仓库下载。
- 域名及 IP 规则为主，不进行基于进程、端口、操作系统名称的分流。

## 文件

| 文件 | 作用 | Mihomo 行为 |
| --- | --- | --- |
| [`rules/custom-reject.yaml`](rules/custom-reject.yaml) | 人工指定阻断域名 | `REJECT` |
| [`rules/custom-proxy.yaml`](rules/custom-proxy.yaml) | 人工补充必须代理的域名 | `WEPC` |
| [`rules/custom-direct.yaml`](rules/custom-direct.yaml) | 人工修复误代理的域名 | `DIRECT` |

三个规则文件均采用 Mihomo `behavior: domain` 对应的 `payload` YAML 格式，可以为空列表 `payload: []`。

## 客户端规则优先级

以下是推荐的客户端规则顺序（由设备本地 YAML 决定，不是本仓库执行的规则）：

1. Jump Desktop 官方域名及本地基础设施 → `DIRECT`
2. `custom-reject` → `REJECT`
3. `custom-proxy` → `WEPC`
4. `custom-direct` → `DIRECT`
5. Loyalsoldier `global-reject` → `REJECT`
6. Loyalsoldier `gfw` → `WEPC`
7. `MATCH,DIRECT`

## 更新方式

本仓库**不运行定时生成任务**，也不再依赖 MetaCubeX 服务白名单构建器。修改并提交本仓库的规则后，客户端通过 Mihomo `rule-providers` 下载更新；当前设备配置的检查间隔为 3600 秒，必要时可在客户端手动更新。

基础 GFW 及广告规则由客户端直接订阅 Loyalsoldier，不在本仓库重复镜像。

## 安全边界

本仓库是公开的域名策略仓库。**禁止提交** WireGuard 私钥、VLESS UUID、REALITY 认证材料、VPS 密钥以及任何设备专属的完整代理配置。

旧架构保存在独立的 `Network-Policy` 仓库，供尚未迁移的客户端继续使用。
