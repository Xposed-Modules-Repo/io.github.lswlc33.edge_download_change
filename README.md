# edge_download_change

LSPosed 模块，适用于 Microsoft Edge 安卓版（`com.microsoft.emmx`）：把 Edge 的下载确认弹窗
替换为**「复制 / 下载」**对话框，并把下载交给**安卓系统下载器**（或选定的第三方下载器 App），
而不是 Edge 自带的下载管理器。

- 作用域：`com.microsoft.emmx`（静态声明）· libxposed API 102 · minSdk 26
- 已在 Edge 153.0.4234.49 上验证
- 签名密钥固定：更新可直接覆盖安装

## 安装

1. 从 [releases](https://github.com/lswlc33/edge_download_change/releases/latest)（稳定版）
   或 **beta 通道**（`-beta.N` 构建）安装 APK；
2. 在 LSPosed 中启用本模块，保持作用域 `com.microsoft.emmx`；
3. 强制停止 Edge 后重新打开；点网页下载链接即出现「复制 / 下载」对话框。

## 源码

源码、文档以及 hook 目标的分析过程：
<https://github.com/lswlc33/edge_download_change>
