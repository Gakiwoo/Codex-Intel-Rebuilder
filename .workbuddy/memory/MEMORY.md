# 长期记忆

## Codex Intel Rebuilder 项目

### 项目目的
将 OpenAI 官方发布的只支持 Apple Silicon/ARM64 的 `Codex.dmg` 重新打包成能在 Intel x86_64 Mac 上运行的 `Codex-Intel.dmg`。

### 技术栈
- Bash 脚本自动化构建
- Electron 应用重打包
- npm 依赖管理和原生模块重编译

### 关键文件
- `Codex Intel.command`: 双击运行的入口文件
- `scripts/build-intel.sh`: 核心构建脚本（约 580 行）
- `README.md` / `README_CN.md`: 项目说明

### 构建流程（10个阶段）
1. 初始化检查依赖工具
2. 挂载源 DMG（只读模式）
3. 校验合法性（codesign 证书验证）
4. 复制 App 到临时工作目录
5. 版本探测（Electron、Codex CLI、原生模块版本）
6. 搭建构建环境（生成 package.json，强制安装 x64 架构依赖）
7. 组装新 App（用 x64 Electron 作为壳）
8. 重编译原生模块（better-sqlite3 和 node-pty）
9. 替换二进制（codex CLI、rg、.node 文件）
10. 签名 + 打包

### 注意事项
- 需要将官方 `Codex.dmg` 放在项目根目录
- npm 安装阶段可能耗时较长（20分钟超时）
- 构建过程完全在临时目录进行，不修改原始 DMG
- 支持旧版 CLT 缺少 `<source_location>` C++ 头文件的兼容问题

### 已知问题与修复
- **版本检测 bug**: 原脚本的正则可能匹配到 Rust 错误字符串 "PoisonError" 混入版本号。已修复为优先提取独立成行的版本号字符串。
- **DMG 挂载冲突**: 多次构建尝试可能导致 DMG 已挂载，需要先卸载。

### 用户偏好
- 用户使用 MacBook Pro
- 熟悉命令行操作
- 正在尝试将 ARM64 的 Codex 应用移植到 Intel Mac