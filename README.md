好的，我参考了 Electron 官方构建产物命名规范和一些行业实践，重新整理了一份更专业、更规范的文档。

---

# Electron 应用多平台安装包命名规范

> 本文档定义了 Electron 应用跨平台构建产物的命名规范，参考 Electron 官方 `dist.zip` 的命名模式，适用于 Windows、macOS、Linux 三大平台。


## 1. 规范说明

### 1.1 为什么要统一命名规范？

- **自动化识别**：CI/CD 流水线可根据文件名自动分类、上传产物
- **用户友好**：用户一眼就能识别适用平台和版本
- **避免混淆**：多架构、多格式文件清晰区分
- **行业标准**：遵循 Electron 官方实践，降低学习成本

### 1.2 基本格式

参考 Electron 官方的命名模式 `electron-v{version}-{platform}-{arch}.zip`：

```
{productName}-v{version}-{platform}-{arch}.{ext}
```

**格式说明**：

| 字段 | 说明 | 示例 |
|------|------|------|
| `productName` | 产品名称，PascalCase | `EBrowser` |
| `v{version}` | 版本号，带 `v` 前缀 | `v1.0.0` |
| `{platform}` | 平台标识，小写 | `windows`、`darwin`、`linux` |
| `{arch}` | 架构标识，小写 | `x64`、`arm64`、`universal` |
| `{ext}` | 扩展名 | `exe`、`dmg`、`AppImage` |

**关键设计决策**：

| 决策点 | 本规范采用 | 说明 |
|--------|-----------|------|
| 平台格式 | `windows` / `darwin` / `linux` | 使用完整名称（对外）或系统标准名（内部）均可，关键是保持一致性 |
| 版本前缀 | 带 `v` | 与 Git tag 保持一致 |
| 架构格式 | `x64` / `arm64` / `universal` | 遵循 Electron 官方规范 |
| 分隔符 | `-` | 连字符，不使用下划线或点 |


## 2. 平台标识

### 2.1 命名规则

| 平台 | Electron 官方标识 | 推荐对外名称 | 说明 |
|------|-----------------------------|-------------|------|
| Windows | `win32` | `windows` | Electron 官方使用 `win32`，对外可用 `windows` |
| macOS | `darwin` | `darwin` | Electron 官方使用 `darwin`，建议保持一致 |
| Linux | `linux` | `linux` | 两者一致 |

**建议**：如果你主要用于 GitHub Releases 分发，使用 `darwin`（与 Electron 官方一致）；如果面向普通用户下载页面，可用 `macos` 更直观。

### 2.2 平台映射表

| 用户友好名 | Electron 官方名 | Node.js `process.platform` |
|-----------|----------------|---------------------------|
| `windows` | `win32` | `win32` |
| `darwin` / `macos` | `darwin` | `darwin` |
| `linux` | `linux` | `linux` |


## 3. 架构标识

### 3.1 标准架构命名

遵循 Electron 官方规范：

| 架构 | 标识 | Node.js `process.arch` | 说明 |
|------|------|------------------------|------|
| 64位 x86 | `x64` | `x64` | 主流选择 |
| 32位 x86 | `ia32` | `ia32` | 兼容旧设备 |
| ARM 64位 | `arm64` | `arm64` | Apple Silicon / ARM 服务器 |
| ARM 32位 | `arm` / `armv7l` | `arm` | 老旧 ARM 设备 |
| macOS 通用 | `universal` | `universal` | 同时包含 x64 + arm64 |

### 3.2 macOS universal 说明

Electron 在 macOS 平台支持构建 `universal` 架构的产物，它会将 `x64` 和 `arm64` 两个架构的二进制文件合并成一个 `.app`，用户无论在哪种 Mac 上都能以原生性能运行。

```
EBrowser-v1.0.0-darwin-universal.dmg
```


## 4. 扩展名与打包格式

| 平台 | 推荐格式 | 扩展名 | electron-builder target |
|------|----------|--------|------------------------|
| Windows | NSIS 安装程序 | `.exe` | `nsis` |
| Windows | 便携版 | `.zip` | `zip` |
| Windows | 应用商店 | `.appx` | `appx` |
| macOS | DMG 镜像 | `.dmg` | `dmg` |
| macOS | ZIP 压缩包 | `.zip` | `zip` |
| Linux | AppImage | `.AppImage` | `AppImage` |
| Linux | Debian 包 | `.deb` | `deb` |
| Linux | RedHat 包 | `.rpm` | `rpm` |


## 5. 完整命名示例

### 5.1 正式版本

```
# Windows
EBrowser-v1.0.0-windows-x64.exe
EBrowser-v1.0.0-windows-ia32.exe
EBrowser-v1.0.0-windows-arm64.exe
EBrowser-v1.0.0-windows-x64.zip

# macOS (使用官方 darwin 标识)
EBrowser-v1.0.0-darwin-x64.dmg
EBrowser-v1.0.0-darwin-arm64.dmg
EBrowser-v1.0.0-darwin-universal.dmg

# Linux
EBrowser-v1.0.0-linux-x64.AppImage
EBrowser-v1.0.0-linux-arm64.AppImage
EBrowser-v1.0.0-linux-x64.deb
EBrowser-v1.0.0-linux-x64.rpm
```

### 5.2 预发布版本

预发布版本在版本号后添加标识（`-alpha.N`、`-beta.N`、`-rc.N`）：

```
EBrowser-v1.0.0-beta.1-windows-x64.exe
EBrowser-v2.0.0-rc.1-darwin-arm64.dmg
EBrowser-v1.0.0-alpha.2-linux-x64.AppImage
```


## 6. Electron 官方产物命名参考

Electron 自身构建产物的命名规范：

| 产物类型 | 命名模式 | 示例 |
|----------|----------|------|
| 主应用包 | `electron-v{VERSION}-{PLATFORM}-{ARCH}.zip` | `electron-v28.0.0-darwin-x64.zip` |
| 符号文件 (macOS) | `electron-v{VERSION}-darwin-{ARCH}-dsym.zip` | `electron-v28.0.0-darwin-arm64-dsym.zip` |
| 符号文件 (Windows) | `electron-v{VERSION}-win32-{ARCH}-pdb.zip` | `electron-v28.0.0-win32-x64-pdb.zip` |
| 符号文件 (Linux) | `electron-v{VERSION}-linux-{ARCH}-debug.zip` | `electron-v28.0.0-linux-arm64-debug.zip` |
| ChromeDriver | `chromedriver-v{VERSION}-{PLATFORM}-{ARCH}.zip` | `chromedriver-v28.0.0-darwin-x64.zip` |
| FFmpeg | `ffmpeg-v{VERSION}-{PLATFORM}-{ARCH}.zip` | `ffmpeg-v28.0.0-linux-x64.zip` |

**关键观察**：

1. 版本号**不带** `v` 前缀（`28.0.0` 而非 `v28.0.0`）
2. 平台使用系统标准名：`darwin`、`win32`、`linux`
3. 扩展名统一为 `.zip`（除了符号文件）
4. 类型标识放在末尾（如 `-dsym`、`-pdb`）

**注意**：Electron 官方的命名是面向**开发工具链**的，而你的应用命名是面向**最终用户**。用户期待看到 `v1.0.0` 这样的版本标识，因此保留 `v` 前缀更合适。


## 7. electron-builder 配置

### 7.1 基础配置（package.json）

```json
{
  "name": "ebrowser",
  "productName": "EBrowser",
  "version": "1.0.0",
  "build": {
    "artifactName": "${productName}-v${version}-${platform}-${arch}.${ext}",
    "mac": {
      "artifactName": "${productName}-v${version}-darwin-${arch}.${ext}"
    },
    "win": {
      "artifactName": "${productName}-v${version}-windows-${arch}.${ext}"
    },
    "linux": {
      "artifactName": "${productName}-v${version}-linux-${arch}.${ext}"
    }
  }
}
```

### 7.2 多架构配置

```json
{
  "build": {
    "mac": {
      "target": [
        { "target": "dmg", "arch": ["x64", "arm64", "universal"] }
      ]
    },
    "win": {
      "target": [
        { "target": "nsis", "arch": ["x64", "ia32"] },
        { "target": "zip", "arch": ["x64"] }
      ]
    },
    "linux": {
      "target": [
        { "target": "AppImage", "arch": ["x64", "arm64"] },
        { "target": "deb", "arch": ["x64"] }
      ]
    }
  }
}
```

### 7.3 electron-builder 占位符变量

| 变量 | 说明 | 示例值（v1.0.0） |
|------|------|-----------------|
| `${productName}` | 产品名 | `EBrowser` |
| `${version}` | 版本号（无 `v`） | `1.0.0` |
| `${platform}` | 平台（builder 内部名） | `win32`、`darwin`、`linux` |
| `${arch}` | 架构 | `x64`、`arm64`、`ia32` |
| `${ext}` | 扩展名 | `exe`、`dmg`、`AppImage`、`deb` |

> **注意**：`${platform}` 在 builder 中返回的是 `win32`、`darwin`、`linux`。如果你希望输出 `windows` 或 `macos`，需要使用脚本自定义或接受 `darwin` 作为 macOS 标识。


## 8. 最佳实践

### 8.1 与 Git Tag 保持一致

```bash
# Git tag 使用带 v 的版本号
git tag v1.0.0
git push --tags

# 构建产物自动使用相同版本号
# EBrowser-v1.0.0-windows-x64.exe
```

### 8.2 中文应用命名处理

如果你的产品名需要显示中文，可以通过 `productName` 和 `executableName` 分离配置：

```json
{
  "productName": "我的浏览器",
  "executableName": "mybrowser",
  "build": {
    "artifactName": "${productName}-v${version}-${platform}-${arch}.${ext}"
  }
}
```

> **注意**：`artifactName` 中的 `${productName}` 会使用 `productName` 字段的值。如果 `productName` 包含中文，建议额外配置 `artifactName` 使用英文名，避免文件名出现非 ASCII 字符。

### 8.3 命名变更与自动更新

⚠️ **重要警告**：安装包的 `artifactName` 一旦变更，会影响自动更新机制。已安装旧版本的用户无法通过自动更新升级到新命名的版本。

**解决方案**：

1. **过渡版本**：在正式切换命名前，发布一个过渡版本同时包含新旧两种命名格式的文件
2. **更新元数据**：修改 `update.yml` 文件，使其指向新的文件名格式
3. **只在重大版本切换**：尽量避免频繁变更命名规范


## 9. 快速参考

### 命名格式速查

```
{产品名}-v{版本号}-{平台}-{架构}.{扩展名}
```

### 平台标识速查

| 场景 | 推荐标识 |
|------|----------|
| 面向用户（下载页面） | `windows`、`macos`、`linux` |
| 面向开发者/CI | `win32`、`darwin`、`linux` |
| 与 Electron 官方保持一致 | `win32`、`darwin`、`linux` |

### 架构标识速查

```
x64    → 64位 Intel/AMD
ia32   → 32位 x86
arm64  → ARM 64位（Apple Silicon / ARM 服务器）
universal → macOS 通用包
```

### 示例汇总

| 平台 | 架构 | 文件名 |
|------|------|--------|
| Windows | x64 | `EBrowser-v1.0.0-windows-x64.exe` |
| Windows | ia32 | `EBrowser-v1.0.0-windows-ia32.exe` |
| macOS | arm64 | `EBrowser-v1.0.0-darwin-arm64.dmg` |
| macOS | universal | `EBrowser-v1.0.0-darwin-universal.dmg` |
| Linux | x64 | `EBrowser-v1.0.0-linux-x64.AppImage` |


## 10. 决策记录

本文档采用以下关键决策：

| 决策点 | 选择 | 理由 |
|--------|------|------|
| 版本号是否带 `v` | 带 `v` | 与 Git tag 保持一致，用户更熟悉 |
| macOS 平台标识 | `darwin` | 与 Electron 官方规范一致 |
| Windows 平台标识 | `windows` | 用户更熟悉，对外友好 |
| 预发布版本格式 | 版本号后加 `-beta.N` | 遵循语义化版本规范 |
| 分隔符 | `-`（连字符） | 可读性好，无歧义 |


## 参考

- [Electron 官方文档 - 应用程序打包](https://www.electronjs.org/zh/docs/latest/tutorial/application-distribution)
- [Electron Build Artifacts - 官方构建产物规范](https://deepwiki.com/electron/electron/6.3-build-artifacts)
- [electron-builder Options 文档](https://git.spacemit.com/electron/electron-builder/-/blob/1a9caa83ea802e762460e18ca08aacac0dd9635c/docs/Options.md)
- [electron-builder AppX 配置](https://builder.electron.js.cn/appx)
