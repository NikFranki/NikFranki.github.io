---
layout:     post
title:      "macOS 科学上网终极方案：Clash Party + TUN 模式"
date:       2026-06-20
subtitle:   "浏览器、终端、Docker 全流量自动分流，零配置，开机自启（⚠️ 已知微信兼容性问题，见正文）"
author:     "franki"
header-img: ""
catalog: true
tags:
    - 科学上网
    - 工具
---

## 效果

- 浏览器、终端、git、docker、绝大多数应用 → 自动分流，零配置
- 国内直连，国外强制走澳洲（澳洲挂了才降级到美日 → 其他地区 → 香港 → DIRECT，全自动）
- 终端无需 `export`，无需任何手动操作，开机自启

---

## ⚠️ 已知缺陷（先看这个，再决定要不要用这套方案）

**TUN 模式下，部分应用会出现连接异常，已确认受影响的：微信（WeChat）。**

表现为：朋友圈图片加载不出来、消息收发卡在"收取中"、文件传输失败。

### 根本原因

这是 **mihomo 内核在 macOS 上处理 TUN 网卡转发时的已知 bug**，不是配置问题：

- 社区已有相同问题的官方 issue：[mihomo #1768](https://github.com/MetaCubeX/mihomo/issues/1768)、[clash-verge-rev #5530](https://github.com/clash-verge-rev/clash-verge-rev/issues/5530)、[clash-verge-rev #1762](https://github.com/clash-verge-rev/clash-verge-rev/issues/1762)（Windows 上同样问题）
- 表现为 TCP 连接能建立、ping 也通，但 **TLS 握手阶段失败**（`SSL_ERROR_SYSCALL`），即使规则判断为 `DIRECT` 直连也无法避免——因为流量依然要先经过 TUN 网卡的转发层，问题出在这一层，不是规则判断错了

### 尝试过的修复方法（均无效，记录下来避免重复踩坑）

| 尝试方法 | 结果 |
|---|---|
| `PROCESS-NAME,WeChat,DIRECT` 按进程直连 | 规则命中率 87%+，但连接依然卡在 TLS 握手，无效 |
| `ipv6: true` ⇄ `false` 来回切换 | 两种状态下问题依然存在 |
| `tun.stack` 从 `system` 改成 `mixed` | 无效 |
| 重启电脑、清理残留 utun 网卡 | 无效（那些 utun 网卡是系统正常占用，不是残留垃圾）|
| 调整 MTU | 未见明显改善 |

结论：**这是内核层面的缺陷，不是配置能解决的，只能等官方修复，或者绕开使用场景。**

### 临时绕开方法

如果你需要使用微信（或其他疑似受影响的 App）：

```
1. Clash Party 左侧「虚拟网卡」开关 → 关闭
2. 正常使用微信
3. 用完后 → 重新打开「虚拟网卡」
```

如果微信是你高频使用的 App，不想每次手动切换，建议直接使用**方案二：系统代理模式**（不会有此问题，原理上完全绕开了 TUN 网卡转发层）。

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

> **为什么用 fallback 而不是 url-test？**
> `url-test` 是纯延迟比拼，日本延迟低会抢走澳洲的位置，但延迟低不等于网速快。
> `fallback` 是强制顺序：澳洲节点只要有一个活着就不切，保证始终走澳洲。整条链路全程自动降级，不需要手动切换任何代理组。

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

## 日常管理

Clash Party 左侧面板提供所有管理功能，不需要 Web 面板：

- **代理组** → 查看节点延迟、手动切换节点
- **外部资源** → 手动刷新订阅节点
- **规则** → 查看当前分流规则
- **连接** → 查看实时流量和连接详情

**机场挂了怎么办**：整条国外节点链路全自动降级（澳洲→美日→其他地区→香港→DIRECT），通常不需要手动干预。如果想强制使用某个具体节点，可以在「代理组」里打开 fallback 链路中的任意一层手动点选。

**临时关闭**：左侧「虚拟网卡」开关关掉 → 网络自动恢复直连，国内正常访问。

**使用微信等受影响的 App**：见上方「⚠️ 已知缺陷」章节，需要先关虚拟网卡。

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

---

## 安全层级（最差情况兜底）

| 情况 | 国内网站 | 国外网站 |
|------|---------|---------|
| 一切正常 | ✅ 直连极速 | ✅ 澳洲节点（自动） |
| 澳洲节点全挂 | ✅ 直连正常 | ✅ 自动切美日（全自动） |
| 澳洲+美日全挂 | ✅ 直连正常 | ✅ 自动切其他地区（全自动） |
| 上面也全挂 | ✅ 直连正常 | ✅ 自动切香港（全自动） |
| 机场彻底全挂 | ✅ 直连正常 | ✅ 自动切 DIRECT（全自动） |
| Mihomo 崩溃 | ✅ 自动重启恢复 | ✅ 恢复后正常 |
| 手动关闭 TUN | ✅ 直连正常 | ❌ 无代理 |
| **使用微信等受影响 App** | ✅ 直连正常 | ⚠️ **需先手动关闭 TUN** |

**国内网站在任何情况下都能访问，这是你的兜底。**

---

## 完整配置文件

> **注意**：以下配置仅供参考，这是我个人的节点优先级策略（澳洲优先 → 美日备选 → 香港最后），不代表适合所有人，请根据自己的机场节点情况灵活调整。

```yaml
mixed-port: 7890
allow-lan: false
mode: rule
log-level: info
ipv6: false   # 保持 false，开 true 会有另一组 IPv6 直连相关问题

# ── TUN 模式（核心：全流量透明代理，终端无需任何配置）────────
tun:
  enable: true
  stack: system
  auto-route: true
  auto-detect-interface: true
  dns-hijack:
    - any:53

# ── DNS（防泄漏）────────────────────────────────────────────
dns:
  enable: true
  ipv6: false
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
  # 澳洲节点池：从所有澳洲节点里自动测速选最快
  - name: "🇦🇺 澳洲优先"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)澳大利亚|\\bAU\\b"
    url: https://www.gstatic.com/generate_204
    interval: 180
    tolerance: 50

  # 美日节点池：澳洲全挂时的备选
  - name: "🚀 美日备选"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)日本|美国|\\bUS\\b|\\bJP\\b|\\bTY\\b|\\bOS\\b|\\bGS\\b"
    url: https://www.gstatic.com/generate_204
    interval: 180
    tolerance: 50

  # 总出口：强制走澳洲，澳洲全挂自动降级到美日
  - name: "🚀 自动选优"
    type: fallback
    proxies:
      - 🇦🇺 澳洲优先
      - 🚀 美日备选
    url: https://www.gstatic.com/generate_204
    interval: 60

  # 其他地区（荷兰、德国、英国、新加坡、台湾）
  - name: "🌐 其他地区"
    type: url-test
    use:
      - 我的机场
    filter: "(?i)荷兰|德国|英国|新加坡|台湾|\\bNL\\b|\\bDE\\b|\\bUK\\b|\\bSG\\b|\\bTW\\b"
    url: https://www.gstatic.com/generate_204
    interval: 300

  # 最后备用：香港，万不得已才选
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

  # 兜底：未匹配流量走这里，全自动降级链路
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
