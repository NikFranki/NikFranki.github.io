---
layout:     post
title:      "macOS 科学上网终极方案：Clash Party + TUN 模式"
date:       2026-06-20
subtitle:   "浏览器、终端、Docker 全流量自动分流，零配置，开机自启"
author:     "franki"
header-img: ""
catalog: true
tags:
    - 科学上网
    - 工具
---

## 效果

- 浏览器、终端、git、docker、所有应用 → 自动分流，零配置
- 国内直连，国外强制走澳洲（澳洲挂了才降级到美日），香港最低优先级
- 终端无需 `export`，无需 `proxychains`，开机自启

---

## 架构

```
所有流量（浏览器 / 终端 / git / docker / 任意应用）
                    ↓
           utun 虚拟网卡（TUN 模式）
           系统级接管，无需任何 export
                    ↓
           Mihomo 规则引擎
           GEOSITE + GEOIP 数据库判断归属
                ↙         ↘
        国内流量            国外流量
        GEOSITE,CN          MATCH → 🌍 兜底代理组
        GEOIP,CN
           ↓                      ↓
        直连                 🌍 兜底（fallback，✅ 全自动降级）
        零延迟                    │
                        ┌─────────┴─────────┐
                        │  1. 🚀 自动选优     │
                        │     ├ 🇦🇺 澳洲优先  │ ← 强制首选
                        │     └ 🚀 美日备选   │ ← 澳洲挂自动切
                        │  2. 🌐 其他地区     │ ← 澳洲+美日都挂自动切
                        │     荷兰/德国/英国   │
                        │     /新加坡/台湾    │
                        │  3. 🇭🇰 香港备用    │ ← 上面全挂才轮到
                        │  4. DIRECT         │ ← 机场彻底全挂兜底
                        └────────────────────┘
```

> **全程自动，整条链路无需手动干预。**
> `fallback` 顺序优先：永远先试第一个，完全不通才降级到下一个。澳洲或美日只要有一个活着，就不会碰到「其他地区」或「香港」，不存在互相抢占的情况。机场彻底全挂时自动落到 `DIRECT`，国内网站依然正常。

> **为什么用 fallback 而不是 url-test？**
> `url-test` 是纯延迟比拼，日本延迟低会抢走澳洲的位置，但延迟低不等于网速快。
> `fallback` 是强制顺序：澳洲节点只要有一个活着就不切，保证始终走澳洲。

> 想强制使用某个具体节点，可以在 Clash Party「代理组」里打开 fallback 链路中的任意一层（比如「🚀 自动选优」「🌐 其他地区」「🇭🇰 香港备用」），里面可以手动点选节点。

---

## 第一步：安装 Clash Party

> 注意：`mihomo-party` 这个 cask 名已更名为 `clash-party`，两者是同一个软件，内核完全一致。

```bash
brew install --cask clash-party
```

没有 Homebrew 先装：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

## 第二步：填写配置文件

打开配置文件，找到这一行：

```yaml
url: "【填写】你的 Clash/mihomo 订阅链接"
```

替换为你机场的 Clash/mihomo 订阅链接，保存。

---

## 第三步：导入配置

1. 打开 Clash Party
2. 左侧面板找到「空白配置」卡片 → 点右侧 `≡` 图标 → 选「编辑」
3. 把配置文件的全部内容粘贴进去 → 保存

---

## 第四步：启动

1. 左侧面板点击刚导入的配置，确保它被选中（高亮状态）
2. 左侧「虚拟网卡」开关 → 打开（即 TUN 模式，会弹出密码框，输入 macOS 登录密码，正常的权限请求）

> 「系统代理」开关**不需要打开**。TUN 模式在网卡层直接接管所有流量，比系统代理更底层，单独开虚拟网卡就够了。

启动后，左侧切换到「代理组」，可以看到所有代理组及其当前选中节点，最终效果如下：

![Clash Party 代理组与节点](/images/posts/proxy/clash-party-proxy-groups.png)

---

## 第五步：验证

```bash
# 打开新终端，直接跑，不需要任何 export：
curl ipinfo.io
# 应该显示澳洲的 IP（country: AU）

# 国内直连测试：
ping baidu.com
# 延迟应该很低（<50ms）
```

---

## 📝 历史坑记录：IPv6 导致部分网站打不开（已修复）

### 曾经的现象

终端 `curl` 正常，Safari 能打开，但 Chrome 等浏览器访问某些网站（如 `weread.qq.com`）报 `ERR_CONNECTION_CLOSED`。

### 根本原因

```
浏览器发起请求
      ↓
系统 DNS 解析（不经过 Mihomo）
      ↓
拿到结果：同时有 IPv6 和 IPv4 地址
      ↓
浏览器优先尝试 IPv6（Happy Eyeballs 算法默认偏好）
      ↓
配置文件里写了 ipv6: false
      ↓
Mihomo 不处理 IPv6 流量，直接放行
      ↓
这条连接绕过了 TUN 接管，走的是系统原生 IPv6 出口
      ↓
裸连国内服务器，被当成异常连接拒绝
      ↓
ERR_CONNECTION_CLOSED
```

简单说：**配置文件的 `ipv6: false` 只管得了 Mihomo 内部，管不了系统底层的 IPv6 偏好。** 终端的 curl 默认更保守、优先用 IPv4，所以没事；浏览器的 Happy Eyeballs 算法会主动抢 IPv6，直接绕开了 Mihomo 分流。

### 当时的临时方案（已废弃）

最初用的是「开 TUN 时手动关闭系统 IPv6」，靠 `tun-on`/`tun-off` 两个 alias 手动切换。这个方案能用，但每次开关 TUN 都要记得多敲一行命令，治标不治本。

### 最终方案：ipv6: true

现在配置文件里直接把顶层 `ipv6` 和 `dns.ipv6` 都设为 `true`，让 Mihomo 原生接管 IPv6 流量，不再需要关闭系统 IPv6，也不需要任何 alias 或后台脚本。

```yaml
ipv6: true

dns:
  enable: true
  ipv6: true
  ...
```

实测验证：

```bash
curl -6 https://ipv6.google.com   # IPv6 连接正常
curl ipinfo.io                     # IPv4 出口正常，仍是澳洲节点
```

两者都通，微信读书等之前受影响的网站也恢复正常，问题彻底解决。

> Mihomo 社区有反馈称 TUN 模式下 IPv6 处理在某些场景（仅 IPv6 网络）存在已知 bug，但国内绝大多数网络是 IPv4+IPv6 双栈，即使 IPv6 处理偶有异常，IPv4 仍会兜底，不会导致断网。

---

## 日常管理

Clash Party 左侧面板提供所有管理功能，不需要 Web 面板：

- **代理组** → 查看节点延迟、手动切换节点
- **外部资源** → 手动刷新订阅节点
- **规则** → 查看当前分流规则
- **连接** → 查看实时流量和连接详情

**机场挂了怎么办**：左侧「代理组」→ 找到「🌍 兜底」→ 手动切换到 `DIRECT`，全部直连，等机场恢复再切回来。

**临时关闭**：左侧「虚拟网卡」开关关掉 → 网络自动恢复直连，国内正常访问。

---

## GEO 数据库（国内外判断依据）

Mihomo 依赖两个数据库来判断流量走直连还是代理：

### GEOSITE — 域名数据库

判断 `google.com`、`baidu.com` 这类**域名**归属。数据来自 [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community) 开源项目，社区长期维护，收录数万个域名分类：

```
GEOSITE,CN     → 百度、淘宝、微信、B站、知乎...
GEOSITE,Google → google.com、gmail.com、youtube.com...
GEOSITE,GitHub → github.com、githubusercontent.com...
```

### GEOIP — IP 数据库

判断一个 **IP 地址**属于哪个国家，本质是一份 IP 段归属表：

```
1.2.4.0/24      → 中国
8.8.8.8         → 美国（Google DNS）
13.107.42.0/24  → 美国（微软）
```

### 判断流程

```
你访问一个地址
      ↓
先查 GEOSITE（域名匹配）
      ↓ 没匹配到
DNS 解析拿到 IP，再查 GEOIP
      ↓
命中 CN → 直连
未命中  → 走代理
```

### 更新方式

**完全自动，无需手动操作。** 每次 Mihomo 启动时自动检查并拉取最新版本，更新失败就继续用本地缓存，不影响正常使用。

如需手动触发：菜单栏图标 → **更新 GEO 数据库**。

> 注意：第一次启动时本地还没有数据库文件，Mihomo 会自动下载。此时 TUN 模式已接管流量，下载本身也走代理，一般几秒内完成，无需干预。

---

## 安全层级（最差情况兜底）

| 情况 | 国内网站 | 国外网站 |
|------|---------|---------|
| 一切正常 | ✅ 直连极速 | ✅ 澳洲节点（自动） |
| 澳洲节点全挂 | ✅ 直连正常 | ✅ 自动切美日（**全自动**） |
| 澳洲+美日全挂 | ✅ 直连正常 | ✅ 自动切其他地区（**全自动**） |
| 上面也全挂 | ✅ 直连正常 | ✅ 自动切香港（**全自动**） |
| 机场彻底全挂 | ✅ 直连正常 | ✅ 自动切 DIRECT（**全自动**） |
| Mihomo 崩溃 | ✅ 自动重启恢复 | ✅ 恢复后正常 |
| 手动关闭 TUN | ✅ 直连正常 | ❌ 无代理 |

**国内网站在任何情况下都能访问，这是你的兜底。**

整条国外节点链路现在全程自动降级，从澳洲一路到 DIRECT，不需要手动切换任何代理组。如果想强制使用某个具体节点，可以在 Clash Party「代理组」里打开 fallback 链路中的任意一层（比如「🚀 自动选优」「🌐 其他地区」「🇭🇰 香港备用」），里面可以手动点选具体节点。

以后如果需要给特定网站单独指定走某个节点，可以新建一个 `select` 类型的代理组，再加一条 `DOMAIN-SUFFIX,网站.com,组名` 规则绑定。

---

## 完整配置文件

> **注意**：以下配置仅供参考，这是我个人的节点优先级策略（澳洲优先 → 美日备选 → 香港最后），不代表适合所有人，请根据自己的机场节点情况灵活调整。

```yaml
mixed-port: 7890
allow-lan: false
mode: rule
log-level: info
ipv6: true

tun:
  enable: true
  stack: system
  auto-route: true
  auto-detect-interface: true
  dns-hijack:
    - any:53

dns:
  enable: true
  ipv6: true
  listen: 0.0.0.0:53
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  fake-ip-filter:
    - "*.lan"
    - "*.local"
    - "+.stun.*.*"
    - "+.stun.*.*.*"
  nameserver:
    - 223.5.5.5
    - 114.114.114.114
  fallback:
    - https://1.1.1.1/dns-query
    - https://8.8.8.8/dns-query
  fallback-filter:
    geoip: true
    geoip-code: CN

proxy-providers:
  我的机场:
    type: http
    url: "【填写】你的 Clash/mihomo 订阅链接"
    interval: 3600
    path: ./providers/airport.yaml
    health-check:
      enable: true
      interval: 300
      url: https://www.gstatic.com/generate_204
      timeout: 3000

proxy-groups:
  - name: "🇦🇺 澳洲优先"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)澳大利亚|\\bAU\\b"
    url: https://www.gstatic.com/generate_204
    interval: 180
    tolerance: 50

  - name: "🚀 美日备选"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)日本|美国|\\bUS\\b|\\bJP\\b|\\bTY\\b|\\bOS\\b|\\bGS\\b"
    url: https://www.gstatic.com/generate_204
    interval: 180
    tolerance: 50

  - name: "🚀 自动选优"
    type: fallback
    proxies:
      - 🇦🇺 澳洲优先
      - 🚀 美日备选
    url: https://www.gstatic.com/generate_204
    interval: 60

  - name: "🌐 其他地区"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)荷兰|德国|英国|新加坡|台湾|\\bNL\\b|\\bDE\\b|\\bUK\\b|\\bSG\\b|\\bTW\\b"
    url: https://www.gstatic.com/generate_204
    interval: 300

  - name: "🇭🇰 香港备用"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)香港|\\bHK\\b"
    url: https://www.gstatic.com/generate_204
    interval: 300

  - name: "🇨🇳 国内直连"
    type: select
    proxies:
      - DIRECT

  - name: "🌍 兜底"
    type: fallback
    proxies:
      - 🚀 自动选优
      - 🌐 其他地区
      - 🇭🇰 香港备用
      - DIRECT
    url: https://www.gstatic.com/generate_204
    interval: 60

rules:
  - DOMAIN-SUFFIX,local,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT
  - IP-CIDR,192.168.0.0/16,DIRECT
  - IP-CIDR,10.0.0.0/8,DIRECT
  - IP-CIDR,172.16.0.0/12,DIRECT
  - GEOSITE,CN,🇨🇳 国内直连
  - GEOIP,CN,🇨🇳 国内直连
  - MATCH,🌍 兜底
```
