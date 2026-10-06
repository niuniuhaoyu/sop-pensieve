# SOP：远程传文件（Mac → Windows 上的 OpenCode）

> 场景：Windows 跑 OpenCode server，Mac / iPhone 通过 Tailscale 连上同一个 session。
> 症状：能从 Mac 发消息，但**发文件/附件不行**。

## 一句话根因

**附件是在「服务端」（Windows）解析的，不是在你 Mac 上解析。**
- `file://` 附件 = 绝对路径，**必须是 Windows 端能读到的文件**；Mac 路径在 Windows 上不存在 → 被拒。
- `data:` 附件 = 内容 base64 内联，**字节随请求走** → 能跨机器。
- HTTP/HTTPS 文件链接 **不支持**。

（Tailscale 本身没问题——能发消息就说明链路是通的。）

## 能用 / 不能用

| 从 Mac 发文件的方式 | 行不行 | 原因 |
|---|---|---|
| 浏览器 Web UI 拖入 / 粘贴 | ✅ | 浏览器只能给字节 → `data:` |
| 桌面版「Attach file」按钮 | ⚠️ | 读本地文件内联就行；发路径就失败 |
| 输入框 `@` 一个 Mac 文件 | ❌ | 变成 `file://` 路径，Windows 读不到 |
| 直接把 Mac 绝对路径发给我 | ❌ | agent 跑在 Windows，只读 Windows 路径 |
| 贴 http/https 文件链接 | ❌ | 不支持 |
| **文件先在 Windows 上，再给我路径** | ✅✅ | 最稳，任何格式都能读 |

## 推荐做法（按省事程度）

1. **Web UI 拖拽**：Mac 浏览器开 `http://<win-tailscale-ip>:49374`（或 `http://windows.<tailnet>.ts.net:49374`），登录后把文件拖进输入框。
2. **Taildrop 先把文件丢到 Windows**，再让我按 Windows 路径读：
   ```bash
   # Mac 上
   tailscale file cp <文件> windows:
   # Windows 端接收（默认落到「下载」），然后把路径发我
   ```
3. **共享文件夹**：把文件放进 `D:\OpenCode\...`，把 Windows 路径发我（我用 `read` 读，PDF/pptx/zip 都行）。
4. 纯文本**直接复制粘贴**进对话。
5. 程序化：`opencode api post /api/session/{id}/prompt`，用 `data:` URL 内联。

## 限制（不是 Tailscale 的锅）

- 单项 **≤ 20 MiB**，桌面多选合计 ≤ 20 MiB；
- 只有 **UTF-8 文本 / 目录 / PNG·JPEG·GIF·WebP** 会真正进模型；
- **PDF、PPTX、ZIP、音频、视频** 等二进制**不会**进请求；
- 图片还需模型支持图像输入，否则换视觉模型。

## 诊断

```powershell
# Windows 端：Tailscale 登录状态 / serve / 监听端口
& "C:\Program Files\Tailscale\tailscale.exe" status
& "C:\Program Files\Tailscale\tailscale.exe" serve status
Get-NetTCPConnection -State Listen | Where-Object LocalPort -in 49374,4096
opencode service status
opencode api get /api/info
```
- 症状：能聊不能传 → 就是附件解析问题（见上）。
- 完全连不上 → 查 Tailscale 登录 / 防火墙只放行 `100.64.0.0/10` / server 是否 `0.0.0.0`。

## 参考

- 本工作区：`opencode-remote-access/TAILSCALE.md`、`opencode-remote-access/README.md`。
- OpenCode V2 文档：`Attachments`、`CLI → Web`。
