# EDC - edge_download_change

[![Nightly](https://github.com/lswlc33/edge_download_change/actions/workflows/nightly.yml/badge.svg)](https://github.com/lswlc33/edge_download_change/actions/workflows/nightly.yml)
[![Release](https://github.com/lswlc33/edge_download_change/actions/workflows/release.yml/badge.svg)](https://github.com/lswlc33/edge_download_change/actions/workflows/release.yml)
[![Latest release](https://img.shields.io/github/v/release/lswlc33/edge_download_change?display_name=tag)](https://github.com/lswlc33/edge_download_change/releases/latest)

把 **Microsoft Edge for Android**（`com.microsoft.emmx`）的下载确认弹窗，换成模块自己的
**「复制 / 下载」**对话框，并让下载交给**系统下载器**（Android DownloadManager）或你选定的
**第三方下载器**，而不是 Edge 自带的下载管理器。

> 这是一个 LSPosed 模块（libxposed API 102），已验证 Edge **153.0.4234.49**。
> English: [README.en.md](README.en.md) ｜ 开发/原理细节: [lsp_module/README.md](lsp_module/README.md)
> ｜ hook 目标的推导过程: [analysis/README.md](analysis/README.md)

---

## 一、使用前景

Edge 安卓版是个好浏览器，但它的**下载管理器一直比较弱**：下载能力完全绑定在浏览器内部，
不能交给系统或其他下载器，通知栏与下载列表的体验也一般，遇到大文件、断点续传、
需要多线程加速的场景就很吃力。

这个模块适合你，如果你：

- 想把网页下载**交给系统下载器**（通知栏显示进度、系统 Downloads 应用里统一管理）；
- 装了 **ADM / IDM / 1DM / FDM / Gopeed / Aria2App** 等下载器，希望网页下载直接进它们；
- 习惯**先看到真实下载链接**再决定下不下（核对域名、避免误点下载、把链接贴给别人）；
- 喜欢"**不满意就自己改**"：模块提供了拦截开关、下载目标选择、状态自检和日志；
- 或者单纯想研究"**Chromium 内核浏览器的下载链路是怎么被接管的**"（`analysis/` 里有完整方法）。

> 注意：需要设备已 root 且安装了支持 **libxposed API 102** 的框架（如 LSPosed）。
> 没有 root / 不用 Xposed 的用户，本模块无法工作。

## 二、解决了什么问题

| 原来的问题 | 现在的行为 |
|---|---|
| 下载只能由 Edge 自己完成，无法交给系统/第三方下载器 | 弹窗里点「下载」→ 由**系统下载器**或**你选的第三方下载器**创建下载任务 |
| Edge 的确认弹窗只给文件名和大小，看不到真实链接 | 我们的弹窗**显示完整 URL**，并提供「复制」一键复制 |
| 点了取消/复制，Edge 仍可能在后台跑流量 | Edge 的下载项在**创建瞬间就被取消**（早于响应体传输），不会偷偷下载 |
| 需要登录 Cookie 的链接，交给系统下载器可能失败 | 提供「复制」通路：复制链接后粘贴到已登录的工具下载（**已知限制**） |
| 装完模块不知道有没有生效 | 状态页显示**模块版本 / 是否已注入 / 最近注入时间 / 框架版本**，并给出排查清单 |
| 出问题只能猜 | 日志页记录每次拦截、跳过（含原因）与下载跳转结果，可复制导出 |

## 三、使用方法

**前置条件**：Android 8.0+；支持 libxposed API 102 的 Xposed 框架（LSPosed 等）；
Edge for Android（`com.microsoft.emmx`）。

1. **安装模块**：
   - **稳定版**：从 [Releases](https://github.com/lswlc33/edge_download_change/releases/latest) 下载最新 APK（tag 形如 `6-2.4`）；
   - **尝鲜版（beta）**：从 [预发布列表](https://github.com/lswlc33/edge_download_change/releases) 下载最新的 `v2.6-beta.N`（版本号带 `beta`，官方模块仓库会把它归入 beta 通道）；
   - 或本地构建：`bash lsp_module/build.sh`（产物 `edge_download_change-<版本>.apk`）。
   两种渠道都用同一把密钥签名，可以互相覆盖安装。
2. **启用模块**：在 LSPosed 管理器里启用本模块。作用域 `com.microsoft.emmx` 已由模块**静态声明**，
   多数管理器会自动应用；如果你的管理器不识别静态作用域，请手动勾选 Edge。
3. **重启 Edge**：**强制停止** Edge（只切后台不算），再重新打开。
4. **验证**：打开模块 App，状态应显示「● 已激活」（含最近注入时间与框架版本）；若未激活，
   状态页有 4 步排查清单，也可以看 LSPosed 日志里的 `EdgeSysDL` 关键字。
5. **日常使用**：在 Edge 里点下载链接 → 弹出「下载此文件？」
   - **下载**：交给当前下载目标（默认系统下载器，通知栏可见进度）；
   - **复制**：复制下载链接，并取消 Edge 内置下载；
   - 点框外/返回：取消，不创建任何下载；
   - **成功时完全无打扰**：不会弹任何 Toast；只有出错或回退（下载器启动失败、链接获取失败、复制失败、
     系统下载器不可用）时才提示一句，细节都在模块日志里。
6. **按需设置**（模块 App 内）：
   - **开启拦截**：关掉后 Edge 完全恢复原生下载行为；
   - **下载目标**：系统下载器 / ADM / IDM / 1DM / FDM / DVGet / Download Navi / Aria2App /
     Gopeed / AB DM / FluxDown / 迅雷，或自定义包名；第三方下载器启动失败会自动回退系统下载器；
   - **日志**：查看/复制/清空。

**常见问题**

- *状态一直"未检测到激活"*：模块没被注入。依次检查：模块是否已启用 → 作用域是否包含 Edge →
  是否**强制停止**过 Edge → 看 LSPosed 日志里有没有 `EdgeSysDL ... module loaded`。
- *点了「下载」但没有下载*：目标下载器未安装或被系统限制；此时模块会自动改用系统下载器并写日志。
- *下载体积很小/是登录页*：该链接需要登录 Cookie，请用「复制」把链接贴到已登录的工具里下载。
- *Edge 升级后失效*：Edge 的混淆字段可能变化；`analysis/README.md` 有重新确认 hook 目标的方法。


## 项目结构

```
edge_download_change/
├── README.md / README.en.md     # 中文 / 英文说明
├── lsp_module/                  # LSPosed 模块工程（源码、资源、构建脚本、开发文档）
└── analysis/                    # Edge 下载链路的分析脚本与结论（含 Via 浏览器参照分析）
```

## 许可与致谢

- `lsp_module/xposed_api/` 内的源码来自 **libxposed API 102**（`io.github.libxposed:api:102.0.0`，Apache-2.0），
  仅作编译期依赖，不会打包进 APK；
- 模块中引用的 Edge / Chromium 类名与字段名归其各自所有者；
- 本模块仅用于个人设备上的合法用途。
