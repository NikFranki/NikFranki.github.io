---
layout:     post
title:      "macOS：在 VS Code 终端中用 Cmd+V 给 Codex 粘贴图片"
date:       2026-08-11
subtitle:   "不改变使用习惯：图片和文本都使用 Cmd+V，退出 Codex 后自动恢复普通终端粘贴"
author:     "franki"
header-img: ""
catalog: true
tags:
    - Codex
    - VS Code
    - macOS
    - 工具
---

## 效果

在 macOS 的 VS Code 集成终端中使用 Codex CLI 时：

- 复制截图或图片后按 `Cmd+V` → 图片附加到 Codex 输入框
- 复制普通文本后按 `Cmd+V` → 正常粘贴文本
- 切换到普通 zsh 终端 → `Cmd+V` 仍然是普通文本粘贴
- 退出 Codex → 不需要手动切换任何设置
- 同时打开多个终端 → 只对当前正在运行 Codex 的终端生效

不需要记忆 `Ctrl+V` 或 `Cmd+Shift+V`，继续使用 macOS 最常见的粘贴快捷键即可。

---

## 前提条件

- macOS
- VS Code 1.93 或更高版本
- 已安装 Codex CLI
- 使用 VS Code 集成终端运行 Codex

---

## 第一步：安装扩展

扩展名称：

```text
Codex Smart Image Paste
```

### 从 VS Code Marketplace 安装

1. 打开 VS Code
2. 按 `Cmd+Shift+X` 打开 Extensions
3. 搜索 `Codex Smart Image Paste`
4. 确认 Publisher 为 `Franki`
5. 点击 Install

也可以打开命令面板并执行：

```text
Extensions: Install Extensions
```

项目地址：<https://github.com/NikFranki/codex-smart-image-paste>

### 使用 VSIX 安装

如果拿到的是 `.vsix` 安装包：

1. 按 `Cmd+Shift+P` 打开命令面板
2. 运行 `Extensions: Install from VSIX...`
3. 选择 `codex-smart-image-paste-*.vsix`
4. 安装完成后运行 `Developer: Reload Window`

---

## 第二步：启动 Codex

安装扩展后，打开一个新的 VS Code 集成终端：

```bash
codex
```

Codex 启动后，VS Code 右下角状态栏会出现：

```text
Codex: smart ⌘V
```

看到这个状态说明扩展已经识别到当前 Codex 会话。

---

## 第三步：使用 Cmd+V 粘贴图片

可以复制已有图片，也可以使用 macOS 截图快捷键直接把截图保存到剪贴板：

```text
Control + Shift + Command + 4
```

框选截图范围后，回到 Codex 输入框，按：

```text
Cmd+V
```

输入框中出现类似下面的内容，就表示图片已经附加成功：

```text
[Image #1]
```

接下来正常输入问题并发送即可，例如：

```text
这张截图里的报错是什么原因？
```

---

## 第四步：粘贴普通文本

在同一个 Codex 会话中，复制一段普通文本，然后继续按：

```text
Cmd+V
```

文本会按照 VS Code 原本的方式粘贴，不需要改用其他快捷键。

---

## 多终端使用

扩展会分别处理每个 VS Code 集成终端：

| 使用位置 | `Cmd+V` 的效果 |
|------|------|
| VS Code 编辑器 | 普通粘贴 |
| 普通 zsh 终端 | 粘贴文本 |
| Codex 终端，剪贴板是文本 | 粘贴文本 |
| Codex 终端，剪贴板是图片 | 附加图片 |
| 退出 Codex 后 | 恢复普通终端粘贴 |

因此可以同时保留普通终端和 Codex 终端，不需要为每个 Tab 手动修改快捷键。

---

## 常见问题

### 安装后没有看到状态栏提示

如果扩展安装时 Codex 已经处于运行状态，请先退出 Codex，然后重新启动：

```bash
exit
codex
```

也可以新建一个 VS Code 集成终端，再运行 `codex`。

### Cmd+V 仍然没有附加图片

确认复制的是图片本身，而不是图片文件名或网页地址。建议先使用 macOS 截图快捷键测试：

```text
Control + Shift + Command + 4
```

然后回到 Codex，按 `Cmd+V`。

### Codex 没有被识别

在 VS Code Settings 中搜索：

```text
Terminal Shell Integration
```

确认 `Terminal › Integrated › Shell Integration: Enabled` 已开启，然后新建终端并重新运行 Codex。

### 使用了自定义 Codex 命令

默认情况下，直接运行 `codex` 会被自动识别。如果通过自定义别名或特殊脚本启动 Codex，可以在 VS Code Settings 中搜索：

```text
Codex Smart Image Paste: Command Pattern
```

根据自己的启动命令调整匹配规则。

### iTerm2 是否支持

这个扩展只作用于 VS Code 集成终端，不会改变 iTerm2、Terminal.app 或其他终端的快捷键。

---

## 卸载

如果不再需要：

1. 按 `Cmd+Shift+X` 打开 Extensions
2. 找到 `Codex Smart Image Paste`
3. 点击 Uninstall
4. 运行 `Developer: Reload Window`

卸载后，VS Code 的 `Cmd+V` 会完全恢复为默认行为，不需要手动清理快捷键配置。

---

## 总结

安装扩展后，日常只需要记住一个操作：

```text
复制文本或图片 → 回到 Codex → Cmd+V
```

扩展地址：<https://github.com/NikFranki/codex-smart-image-paste>
