# SOP：环境与工具链（Stata / R / Python）

> 来源：本机 Windows 的 Stata 19 + stata-mcp + Python 环境搭建经验。
> 原则：**换台机器也能把分析跑起来。**

## 适用

- 新机器 / 换环境后，快速把分析工具链装好并跑通。

## 步骤

1. **Stata**：本机 `D:\Stata\StataMP-64.exe`（Stata 19 MP）。
   - 批处理跑 do（无 GUI）：
     ```powershell
     Start-Process -FilePath "D:\Stata\StataMP-64.exe" `
       -ArgumentList '/e','do','<脚本>.do' -WorkingDirectory "<工作目录>" -Wait
     # 然后读 <脚本>.log
     ```
   - ⚠️ 批处理**遇错即停**；测试脚本里预期可能报错的命令前置 `capture`。
2. **常用包**（缺失会中途中断）：
   ```stata
   ssc install estout, replace     // eststo / esttab 出表
   ssc install reghdfe,  replace
   ssc install ivreg2,   replace
   ```
   报错先 `which <命令>` 确认包在不在。
3. **Python**：本机 `C:\Users\35223\AppData\Local\Programs\Python\Python312\python.exe`。
   - 常用库：`pypdf`（抽 PDF 文字）、`python-pptx`（生成 PPT）、`pandas`。
   - `python -m pip install --user <pkg>`。
4. **stata-mcp**：已接入 opencode（`mcp.servers.stata-mcp`），用于让 agent 直接调 Stata。
5. **记录版本**：Stata 版本、包版本、Python 及库版本写进复现包。

## 检查清单

- [ ] Stata 批处理跑通（`sysuse auto` 冒烟测试）
- [ ] 常用包已装，并在 do 文件顶部声明
- [ ] Python 及所需库可用
- [ ] 版本信息已记录

## 常见坑

- 出表包 `estout` 未装 → 脚本第 5 步 `command eststo is unrecognized`；
- 批处理遇到"预期内的报错"直接停 → 前置 `capture`；
- 路径写死成别的机器 → 改成当前机器或相对路径；
- `python`/`pip` 不在 PATH → 用绝对路径。

## 参考

- `环境脚本/`、[`SOP_案例复现.md`](SOP_案例复现.md)、[`SOP_远程传文件.md`](SOP_远程传文件.md)。
