# 第 56 讲：OpenClaw 连接远程 Chrome 实现搜索与自动化

让你的 AI Agent 操控你电脑上的 Chrome 浏览器，完成搜索、截图、网页自动化等操作

---

## 背景

OpenClaw 自带浏览器控制能力，默认是启动一个隔离的 `openclaw` 浏览器实例。但很多时候我们需要用**本机已登录的 Chrome**——比如你有 Google 账号、已经登录了各种服务，或者想直接用你日常使用的浏览器环境。

通过 Chrome DevTools Protocol (CDP)，OpenClaw 可以远程连接到你电脑上正在运行的 Chrome，就像调试工具一样控制它。

---

## 整体架构

```
┌─────────────────────┐      Tailscale / 局域网       ┌─────────────────────────┐
│  OpenClaw 服务器     │  ──── CDP over HTTP ──────▶  │  Windows 本机 Chrome    │
│  (Linux, 无 GUI)     │                              │  (带调试端口 9222)      │
│                      │  ◀─── JSON / WebSocket ────  │                         │
│  gateway → browser   │                              │  http://100.113.x.x:9222│
│  profile → remote    │                              │                         │
└─────────────────────┘                              └─────────────────────────┘
```

OpenClaw 通过 `cdpUrl` 连接远程 Chrome，本质就是 Chrome DevTools Protocol 的标准用法。

---

## 第一步：Windows 端启动带调试端口的 Chrome

在你的 Windows 电脑上（以管理员身份打开 CMD 或 PowerShell），运行：

```cmd
"C:\Program Files\Google\Chrome\Application\chrome.exe" ^
 --remote-debugging-port=9222 ^
 --remote-debugging-address=0.0.0.0 ^
 --user-data-dir="C:\chrome-debug"
```

### 参数说明

| 参数 | 作用 |
|------|------|
| `--remote-debugging-port=9222` | 开启 CDP 调试端口 |
| `--remote-debugging-address=0.0.0.0` | 允许外部 IP 连接（默认只允许 127.0.0.1） |
| `--user-data-dir="C:\chrome-debug"` | 指定独立用户数据目录，不影响日常 Chrome |

> **注意：** Chrome 启动后会打开一个新窗口，这是正常的调试实例，可以关掉窗口但**不要关进程**。如果要后台运行，启动后最小化即可。

### 验证是否启动成功

在 Windows 本机验证：

```cmd
curl http://127.0.0.1:9222/json/version
```

或者在 OpenClaw 服务器端验证（如果是局域网或 Tailscale 互通）：

```bash
curl http://100.113.116.86:9222/json/version
```

成功会返回类似这样的 JSON：

```json
{
  "Browser": "Chrome/...",
  "Protocol-Version": "1.3",
  "webSocketDebuggerUrl": "ws://100.113.116.86:9222/devtools/..."
}
```

---

## 第二步：OpenClaw 配置远程 Chrome 连接

在 OpenClaw 配置文件 `~/.openclaw/openclaw.json` 中添加一个远程 CDP 浏览器配置：

```json5
{
  "browser": {
    "enabled": true,
    "ssrfPolicy": {
      "dangerouslyAllowPrivateNetwork": true   // ← 关键：允许访问私有网络地址
    },
    "profiles": {
      // OpenClaw 自带的管理型浏览器（隔离环境）
      "openclaw": { "cdpPort": 18800, "color": "#FF4500" },

      // 你的 Windows 远程 Chrome
      "win-chrome": {
        "cdpUrl": "http://100.113.116.86:9222",
        "color": "#4285F4"
      }
    },
    "defaultProfile": "win-chrome"   // 设为默认，方便 Agent 直接用
  }
}
```

### 为什么需要 `dangerouslyAllowPrivateNetwork: true`？

OpenClaw 默认有 SSRF 防护，禁止访问私有网络地址（如 `192.168.x.x`、`10.x.x.x`、`100.x.x.x` 等 Tailscale CGNAT 地址）。你的远程 Chrome 在内网 / Tailscale 上，所以需要显式放行。

如果你不想全局放行，也可以用 `hostnameAllowlist` 更精确地控制：

```json5
"ssrfPolicy": {
  "hostnameAllowlist": ["100.113.116.86"]
}
```

### 配置生效

修改完配置文件后，重启 OpenClaw：

```bash
openclaw gateway restart
```

---

## 第三步：验证远程连接

```bash
# 查看所有可用浏览器配置
openclaw browser profiles

# 测试远程 Chrome 是否可达
openclaw browser --browser-profile win-chrome tabs

# 在远程 Chrome 中打开网页
openclaw browser --browser-profile win-chrome open https://www.google.com

# 查看页面快照（Agent 能读到的内容）
openclaw browser --browser-profile win-chrome snapshot

# 截图
openclaw browser --browser-profile win-chrome screenshot
```

如果一切正常，你会看到远程 Chrome 的标签页信息以及页面内容。

---

## 第四步：让 Agent 用远程 Chrome 搜索

配置好之后，你的 Agent 就可以通过 browser tool 操控远程 Chrome 了。

### 方式一：在聊天中让 Agent 搜索

直接对 OpenClaw 说：

> "用浏览器打开 Google 搜索 harries.blog"

Agent 会自动：
1. 在远程 Chrome 打开新标签页
2. 导航到 Google
3. 输入搜索关键词
4. 读取搜索结果并返回给你

### 方式二：通过 CLI 快速搜索

```bash
openclaw browser --browser-profile win-chrome open https://www.google.com/search?q=harries.blog
openclaw browser --browser-profile win-chrome snapshot --urls
```

### 方式三：Agent 自动化脚本

在 OpenClaw 中可以给 Agent 这样的指令：

```json
{
  "action": "open",
  "url": "https://www.google.com",
  "targetId": "search",
  "profile": "win-chrome"
}
```

---

## 进阶：在 OpenClaw 配置中固定 profile

如果你希望 Agent 默认就用远程 Chrome，把配置设为 `defaultProfile`：

```json5
{
  "browser": {
    "defaultProfile": "win-chrome",
    "profiles": {
      "win-chrome": {
        "cdpUrl": "http://100.113.116.86:9222",
        "color": "#4285F4"
      }
    }
  }
}
```

这样 Agent 在执行 `browser()` 工具调用时，默认就会使用远程 Chrome，无需每次指定 profile。

---

## 常见问题

### Q: Connection reset by peer

端口能通但 Chrome 拒绝连接，最常见的原因是**缺少 `--remote-allow-origins=*`**。

新版 Chrome 默认只允许同源（127.0.0.1）的 CDP 连接，外部 IP 连接会被拒绝。

虽然你在命令中已经加了 `--remote-debugging-address=0.0.0.0`，建议再加一个：

```cmd
chrome --remote-debugging-port=9222 ^
       --remote-debugging-address=0.0.0.0 ^
       --remote-allow-origins=* ^
       --user-data-dir="C:\chrome-debug"
```

### Q: Chrome 启动后没窗口

查看任务管理器确认 `chrome.exe` 进程是否在运行。如果没进程，说明启动失败了，检查路径是否正确。

### Q: 关闭 Chrome 窗口后进程也退了

默认情况下关闭最后一个窗口会退出 Chrome。要在关闭窗口后保持后台运行，启动时加 `--keep-alive-for-test`：

```cmd
chrome --remote-debugging-port=9222 ^
       --remote-debugging-address=0.0.0.0 ^
       --remote-allow-origins=* ^
       --user-data-dir="C:\chrome-debug" ^
       --keep-alive-for-test
```

### Q: OpenClaw 报 SSRF 策略拒绝

```
Navigation blocked by SSRF policy
```

说明 `ssrfPolicy` 没有放行私有网络地址。按上文配置 `dangerouslyAllowPrivateNetwork: true` 或 `hostnameAllowlist` 即可。

### Q: 多台机器怎么管理？

如果你有多台机器都启动了远程 Chrome，可以配置多个 profile：

```json5
"profiles": {
  "win-office": { "cdpUrl": "http://100.113.116.86:9222", "color": "#4285F4" },
  "mac-home":   { "cdpUrl": "http://100.90.80.70:9222",  "color": "#34A853" },
  "linux-vm":   { "cdpUrl": "http://192.168.1.100:9222",  "color": "#EA4335" }
}
```

---

## 最后总结

通过 CDP 实现 OpenClaw 远程操控 Chrome 的关键三点：

| 环节 | 操作 |
|------|------|
| **Windows 端** | Chrome 启动带 `--remote-debugging-port=9222` 和 `--remote-allow-origins=*` |
| **OpenClaw 配置** | 添加 `profiles.<name>.cdpUrl` 指向远程 Chrome，放行 SSRF 策略 |
| **使用** | 通过 browser tool 或 CLI，指定 profile 即可操控远程浏览器 |

这套方案适合：
- ✅ 服务器无 GUI，但需要浏览器能力时
- ✅ 想用本机已登录账号的浏览器环境
- ✅ 需要在多台机器间共享浏览器控制
- ✅ 通过 Tailscale / 内网远程操控浏览器

有了这套配置，你在手机上跟 OpenClaw 说一句"帮我搜一下 XXX"，Agent 就能操控你电脑上的 Chrome 去搜索并把结果发回给你。

---

## 本节作业

1. 在你的 Windows 电脑上启动带 `--remote-debugging-port=9222` 的 Chrome，并验证本机 `curl http://127.0.0.1:9222/json/version` 能返回 JSON。
2. 在 OpenClaw 配置中添加一个远程 CDP profile，指向你的 Windows 电脑地址，重启后运行 `openclaw browser --browser-profile <name> tabs` 确认连接成功。
3. 通过 CLI 让远程 Chrome 导航到 `https://www.google.com` 并截图。
4. 尝试多配置一个 profile 指向不同的远程 Chrome（比如笔记本或另一台机器）。
5. 如果你的 Chrome 关闭窗口后会退出进程，加上 `--keep-alive-for-test` 参数再试一次。

---

## 下一节预告

第 57 讲我们将探索 OpenClaw 的 Node 机制——如何让 Gateway 把浏览器控制、文件操作等任务分发到多台机器的 Node 节点上，实现真正的分布式 Agent 操控。

---

## 参考资料

- [OpenClaw Browser CLI 文档](/cli/browser)
- [OpenClaw Browser 工具文档](/tools/browser)
- [OpenClaw 配置参考 - browser 配置段](/gateway/configuration-reference)
- [OpenClaw 浏览器 Linux 排错](/tools/browser-linux-troubleshooting)
- [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)
- [第 46 讲：网页自动化助手](articles/lesson-46-browser-automation-assistant-flow-zh.md)

---

原文外链：[OpenClaw 连接远程 Chrome 实现搜索与自动化](https://www.harries.blog/archives/720428.html)
