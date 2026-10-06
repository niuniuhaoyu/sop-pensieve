# 推送到 GitHub —— 操作指引

> 本目录 `D:\OpenCode\SOP` 已经是本地 git 仓库（分支 `main`）。
> 只差最后一步：**在 GitHub 建一个空仓库，然后 push**。以下任选一种。

## 方式 A：用 gh（已装便携版）

gh 路径：`C:\Users\35223\AppData\Local\Programs\gh\bin\gh.exe`

```powershell
# 1) 登录（会开浏览器，或用 device code）
& "$env:LOCALAPPDATA\Programs\gh\bin\gh.exe" auth login

# 2) 在当前目录建公开仓库并推送（自动设 remote）
cd D:\OpenCode\SOP
& "$env:LOCALAPPDATA\Programs\gh\bin\gh.exe" repo create sop-pensieve --public --source . --remote origin --push
```

## 方式 B：网页建库 + git push（HTTPS）

1. 打开 https://github.com/new ，仓库名如 `sop-pensieve`，**不要**勾 README/.gitignore（空仓库）。
2. 在本机执行（把 `<用户名>` 换成你的 GitHub 用户名）：

```powershell
cd D:\OpenCode\SOP
git remote add origin https://github.com/<用户名>/sop-pensieve.git
git push -u origin main
# 首次会弹 Git Credential Manager 让你登录/授权
```

> 本机 HTTPS 访问 GitHub 有「证书吊销检查离线」问题，若 push 报 schannel 错，先执行：
> `git config --global http.schannelCheckRevoke false`

## 方式 C：网页建库 + git push（SSH）

```powershell
cd D:\OpenCode\SOP
git remote add origin git@github.com:<用户名>/sop-pensieve.git
git push -u origin main
# 本机已有密钥 ~/.ssh/id_ed25519；若带口令，需交互输入
```

## 完成后

- 仓库首页的 `README.md` 就是「冥想盆」索引，打开即看。
- 以后每次更新 SOP：
  ```powershell
  cd D:\OpenCode\SOP
  git add -A
  git commit -m "SOP: 更新 <主题>"
  git push
  ```

## 注意

- `.gitignore` 已排除数据/媒体/备份，只收文本 SOP。
- 若要公开，注意别把敏感内容写进 SOP。
