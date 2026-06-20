---
layout:     post
title:      "macOS 科学上网方案二：Clash Party + 系统代理模式"
date:       2026-06-21
subtitle:   "不用 TUN 虚拟网卡，微信等 App 完全正常，终端写一次 export 永久生效"
author:     "franki"
header-img: ""
catalog: true
tags:
    - 科学上网
    - 工具
---

## 效果

- 浏览器、GUI App → 自动读取系统代理，零配置
- 终端 → 需要写一次 `export`（写进 `.zshrc` 后永久生效，不用每次手动敲）
- 国内直连，国外强制走澳洲（澳洲挂了才降级到美日 → 其他地区 → 香港 → DIRECT，全自动）
- **微信等 App 不会出现 TUN 模式下的图片加载/连接异常问题**

---

## 这个方案是怎么来的

最初用的是 **TUN 虚拟网卡模式**（见[方案一](/2026/06/20/macOS-科学上网终极方案-Clash-Party-+-TUN-模式/)），实现了终端完全零配置。但实测发现 TUN 模式下微信会出现朋友圈图片加载失败、消息卡在"收取中"的问题，这是 mihomo 内核在 macOS 上处理 TUN 网卡转发时的已知 bug（社区 issue [#1768](https://github.com/MetaCubeX/mihomo/issues/1768)、[#5530](https://github.com/clash-verge-rev/clash-verge-rev/issues/5530)），尝试了按进程直连、调整 IPv6、调整协议栈、重启清网卡等多种方法均未解决。

这个方案改用最传统、最成熟的 **HTTP/SOCKS5 代理协议**（和 trojan-qt5、Shadowrocket 等工具原理一致），不经过 TUN 网卡转发这一层，从根本上避开了这个 bug。代价是终端需要手动配置一次环境变量。

---

## 架构

```
浏览器 / GUI App
      ↓
读取系统代理设置（127.0.0.1:7890）
      ↓
Mihomo 规则引擎（HTTP/SOCKS5 协议层对话，不经过网卡转发）
      ↓
GEOSITE + GEOIP 数据库判断归属
   ↙         ↘
国内流量      国外流量
  ↓              ↓
直连      🌍 兜底（fallback，全自动降级）
              │
    ┌─────────┴─────────┐
    │ 1. 🚀 自动选优      │
    │    ├ 🇦🇺 澳洲优先   │ ← 强制首选
    │    └ 🚀 美日备选    │ ← 澳洲挂自动切
    │ 2. 🌐 其他地区      │ ← 澳洲+美日都挂自动切
    │ 3. 🇭🇰 香港备用     │ ← 上面全挂才轮到
    │ 4. DIRECT          │ ← 机场彻底全挂兜底
    └────────────────────┘

终端（zsh）
      ↓
export http_proxy=http://127.0.0.1:7890（写一次，永久生效）
      ↓
所有命令（curl/git/npm/pip...）自动发给 Mihomo
      ↓
同样的规则引擎分流（和浏览器走的是同一套判断逻辑）
```

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

## 第四步：启动（注意，这里和方案一不同）

1. 左侧面板点击刚导入的配置，确保它被选中（高亮状态）
2. 左侧「系统代理」开关 → 打开
3. 左侧「虚拟网卡」开关 → **保持关闭**（这是和方案一的关键区别）

启动后，左侧切换到「代理组」，可以看到所有代理组及其当前选中节点，最终效果如下：

![Clash Party 系统代理模式 - 代理组与节点](/images/posts/proxy/clash-party-proxy-mode.png)

---

## 第五步：配置终端代理（写一次，永久生效）

```bash
cat >> ~/.zshrc << 'EOF'

# ── Clash Party 系统代理（终端用）──
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export all_proxy=socks5://127.0.0.1:7890
export no_proxy="localhost,127.0.0.1,::1,*.local,192.168.*"

# 临时关闭代理（Mihomo 进程关闭/异常时使用，恢复纯直连）
proxy_off() {
  unset http_proxy https_proxy all_proxy
  echo "代理已关闭，当前直连"
}

# 重新开启代理
proxy_on() {
  export http_proxy=http://127.0.0.1:7890
  export https_proxy=http://127.0.0.1:7890
  export all_proxy=socks5://127.0.0.1:7890
  echo "代理已开启 → 127.0.0.1:7890"
}

# 查看当前出口 IP，快速判断代理状态
proxy_check() {
  curl -s --max-time 5 ipinfo.io | python3 -m json.tool 2>/dev/null || echo "网络不通"
}
EOF

source ~/.zshrc
```

---

## 第六步：验证

```bash
# 国外流量应该走代理（澳洲 IP）
curl ipinfo.io

# 国内流量应该极速直连
curl -w "%{time_total}s\n" -o /dev/null -s https://baidu.com
```

---

## ⚠️ 重要缺点：需要手动判断代理是否有效

这是这个方案相比 TUN 模式最大的代价，必须理解清楚。

### 问题场景

`http_proxy` 这个环境变量一旦写进 `.zshrc`，**每次开新终端都会自动指向 `127.0.0.1:7890`**。

如果 Mihomo 进程本身被关闭了（比如退出 Clash Party、电脑重启后忘记打开、Mihomo 异常崩溃），这个端口就没有任何东西在监听了，但环境变量还在指向它。

后果：**终端会完全无法联网，包括访问国内网站**，因为请求一直被发往一个不存在的端口，而不是回退到直连。

这点和 TUN 模式不同——TUN 模式下哪怕 Mihomo 挂了，国内网站依然能通过其他方式正常访问；但系统代理模式下，环境变量是"硬绑定"的，Mihomo 一挂，终端全挂。

### 怎么判断 + 怎么处理

**判断方法：**
```bash
proxy_check
```
如果返回正常的 IP 信息（澳洲地址），说明代理有效。如果卡住或报错（"网络不通"），说明 Mihomo 没在跑。

**处理方法：**
```bash
proxy_off    # 立刻解除绑定，恢复纯直连，国内网站马上恢复正常
```

等 Clash Party 重新打开、Mihomo 正常运行后，再执行：
```bash
proxy_on     # 重新绑定代理
```

**建议养成的习惯**：每次开机后，如果准备用终端做需要联网的操作（git clone、npm install 等），先确认 Clash Party 已经打开、系统代理已经开启，必要时跑一下 `proxy_check` 心里有底。

---

## 日常管理

Clash Party 左侧面板提供所有管理功能，不需要 Web 面板：

- **代理组** → 查看节点延迟、手动切换节点
- **外部资源** → 手动刷新订阅节点
- **规则** → 查看当前分流规则
- **连接** → 查看实时流量和连接详情

**机场挂了怎么办**：整条国外节点链路全自动降级（澳洲→美日→其他地区→香港→DIRECT），通常不需要手动干预。

**Mihomo 没在跑 / 终端联网异常**：执行 `proxy_off`，立刻恢复直连。

---

## 安全层级（最差情况兜底）

| 情况 | 浏览器/GUI App | 终端 |
|------|---------|---------|
| 一切正常 | ✅ 国内直连/国外澳洲节点 | ✅ 同上 |
| 澳洲节点全挂 | ✅ 自动切美日（全自动） | ✅ 同上 |
| 机场彻底全挂 | ✅ 自动切 DIRECT（全自动） | ✅ 同上 |
| Mihomo 进程关闭/崩溃 | ✅ 系统代理设置失效，App 各自处理（多数会直连或报错，不会卡死全局） | ❌ **终端全部请求失败，需手动执行 `proxy_off`** |
| 使用微信等 App | ✅ 正常，无 TUN 模式那个 bug | — |

**这是方案二相比方案一的核心权衡：用"终端需要手动判断代理状态"换来了"微信等 App 完全正常"。**

---

## 完整配置文件

> **注意**：以下配置仅供参考，这是我个人的节点优先级策略（澳洲优先 → 美日备选 → 香港最后），不代表适合所有人，请根据自己的机场节点情况灵活调整。

```yaml
mixed-port: 7890
allow-lan: false
mode: rule
log-level: info
ipv6: false

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
