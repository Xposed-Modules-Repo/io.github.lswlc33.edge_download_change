# edge_download_change

LSPosed module for Microsoft Edge for Android (`com.microsoft.emmx`): replaces Edge's
download confirmation dialog with a **Copy / Download** dialog and hands the download over
to the **Android system DownloadManager** (or a chosen third-party downloader app) instead
of Edge's own download manager.

- Scope: `com.microsoft.emmx` (declared statically) · libxposed API 102 · minSdk 26
- Verified on Edge 153.0.4234.49
- Signed with a stable key: updates install over previous versions

## Install

1. Install the APK from the [releases](https://github.com/lswlc33/edge_download_change/releases/latest)
   (stable) or from the **beta channel** (`-beta.N` builds).
2. Enable the module in LSPosed, keep the scope `com.microsoft.emmx`.
3. Force-stop Edge and open it again; tapping a download link shows the Copy / Download dialog.

## Source

Source code, documentation and the analysis that was used to derive the hook targets:
<https://github.com/lswlc33/edge_download_change>
