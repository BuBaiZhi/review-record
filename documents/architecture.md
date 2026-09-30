# 项目架构说明

> **记录规则（固定约定，不得违反）**
> 1. 每当**新增文件**、**删除文件**或**改变文件职责**时，必须同步更新本文件。
> 2. 每个文件需记录：路径、职责、关键内容、与其他文件的关系。
> 3. 本文件同样只增不删。文件职责发生迁移时，在「变更记录」中追加说明，不抹掉历史描述。

---

## 一、目录结构总览

```
2026-09-30-13-07-11\                  工作区根目录（也是 Git 仓库根目录）
│
├── .gitignore                         Git 排除规则（工具链、构建产物、依赖）
│
├── .git\                              Git 仓库元数据（不纳入版本控制）
│   ├── config                         本仓库配置：TLS 后端、CA 证书路径、提交身份、远程地址、分支跟踪
│   └── win-root-ca.pem                Windows 根证书库导出件（102 张），用于穿过本机代理访问 GitHub
│
├── documents\                         项目文档
│   ├── progress.md                    开发进度记录，按 1.0 / 1.1 递增，只增不删
│   ├── architecture.md                本文件，记录各文件职责
│   ├── product-requirements.md        需求文档（PRD）：做什么、为什么
│   ├── functional-design.md           功能设计文档：怎么做、界面与数据设计
│   └── development-plan.md            开发计划方案：分阶段划分、交付物与验收标准
│
├── review-app\                        应用源代码
│   └── www\                           前端资源（Capacitor 的 webDir）
│       ├── index.html                 页面结构
│       └── styles.css                 样式
│
└── .android-build\                    编译工具链（临时，非应用代码，约 970M）
    ├── setup.sh                       工具链自动安装脚本
    ├── jdk17.zip                       JDK 安装包（解压后可删）
    ├── cmdline-tools.zip               SDK 工具包（解压后可删）
    ├── cmdtools\                       解压残留目录（可删）
    ├── jdk\jdk-17.0.20.1+1\           JDK 17 运行时
    └── android-sdk\                   Android SDK
        ├── cmdline-tools\             sdkmanager 等命令行工具
        ├── build-tools\               构建工具
        ├── platforms\                 编译目标平台
        └── licenses\                  许可协议确认记录
```

---

## 二、文件类型说明

| 目录 | 是否属于应用 | 是否提交 GitHub | 说明 |
| --- | --- | --- | --- |
| `documents/` | 否 | ✅ 是 | 项目文档 |
| `review-app/` | ✅ 是 | ✅ 是 | 应用全部源代码 |
| `.git/` | 否 | ❌ 否 | Git 元数据目录。其中 `config` 与 `win-root-ca.pem` 是针对**本机网络环境**的配置，换机器需重新生成 |
| `.android-build/` | 否 | ❌ 否 | 仅本机编译用，约 970M，必须排除 |

> **远程仓库**：<https://github.com/BuBaiZhi/review-record.git>　主分支 `main`　（首次推送 2026-09-30，提交 `e392433`）

---

## 三、文件清单

### 1. `documents/progress.md`

- **职责**：记录项目每一次改动。按版本号（1.0、1.1、1.2……）追加快照。
- **关键内容**：项目目标、已确认的技术决策表、每个版本的变化清单、涉及文件、当前可运行状态、待办清单。
- **规则**：只增不删。任何代码修改后必须追加新版本小节。
- **关系**：与 `architecture.md` 互为补充 —— 本文件记"什么时候改了什么"，`architecture.md` 记"每个文件是干什么的"。

### 2. `documents/architecture.md`

- **职责**：记录每个文件的路径与职责，说明整体架构与技术选型。
- **关键内容**：目录结构、文件类型说明、逐文件职责说明、技术选型理由、数据流、待建文件规划。
- **规则**：新增或删除文件时必须更新。
- **关系**：本文件。

### 3. `review-app/www/index.html`

- **职责**：应用的全部页面结构（单页应用，通过切换 class 控制显示）。
- **关键内容**：
  - 顶栏 `header.appbar`：标题 + 「今天」按钮
  - 记录页 `#view-record`：日期显示、麦克风按钮 `#micBtn`、录音状态与计时、原始记录文本框 `#rawText`、「AI 整理成复盘」按钮 `#btnAI`、「跳过 AI 手动填写」按钮 `#btnManual`、整理结果容器 `#organizedWrap`、`#sectionsBox`、保存按钮 `#btnSave`
  - 复盘库页 `#view-library`：统计栏 `#stats`、搜索框 `#searchInput`、列表容器 `#libList`
  - 设置页 `#view-settings`：DeepSeek Key、模型选择、模板栏目编辑 `#tmplBox`、Obsidian 库路径 `#setVault`、导出与备份、数据统计
  - 底部导航 `nav.tabbar`：记录 / 复盘库 / 设置三个标签
  - 详情抽屉 `#sheet`：查看与编辑单篇复盘
  - 操作菜单 `#menu`、提示条 `#toast`
- **依赖**：引用 `styles.css` 与 `app.js`。

### 4. `review-app/www/styles.css`

- **职责**：应用全部样式。
- **关键内容**：
  - CSS 变量定义配色与圆角（`--accent` 墨绿主色、`--bg` 米白背景）
  - 刘海屏安全区适配（`--safe-t` / `--safe-b`，使用 `env(safe-area-inset-*)`）
  - 录音按钮脉冲动画 `.mic-btn.rec` + `@keyframes pulse`
  - 卡片、按钮、表单、列表、月份分组、统计卡、详情抽屉、菜单、Toast 的全部样式
  - 底部导航固定定位 `position:fixed`
- **依赖**：被 `index.html` 引用。

### 5. `.android-build/setup.sh`

- **职责**：一键下载并安装 APK 编译所需的 JDK 与 Android SDK。
- **关键内容**：
  - 从 `api.adoptium.net` 拉取 Temurin JDK 17
  - 从 `dl.google.com` 拉取 Android 命令行工具
  - 用 Python 的 `zipfile` 解压（Git Bash 无 unzip）
  - 通过 `sdkmanager` 安装 `platform-tools`、`platforms;android-34`、`build-tools;34.0.0` 并接受许可
- **规则**：仅在需要重新搭建编译环境时运行，不属于应用代码，不提交 GitHub。

### 6. `.gitignore`（2026-09-30 新增）

- **职责**：指定 Git 提交时需要排除的文件与目录，避免把编译工具链和构建产物推送到 GitHub。
- **关键内容**：
  - 排除 `.android-build/`（约 970M 的编译工具链）
  - 排除 `*.zip` / `*.tar.gz`（下载的安装包）
  - 排除 `node_modules/`（Node 依赖）
  - 排除 `review-app/android/` 下的构建产物（`build/`、`.gradle/`、`local.properties` 等）
  - 排除 `*.apk` / `*.aab` / `*.jks` / `*.keystore`（安装包与签名文件）
  - 排除 `.workbuddy/`（WorkBuddy 工作区状态）
  - 排除系统与编辑器临时文件
- **效果**：仓库中仅保留 `documents/`、`review-app/www/`、`.gitignore` 三类内容。
- **规则**：新增需要排除的目录时，需同步更新本文件说明。

### 7. `.git/config`（2026-09-30 新增）

- **职责**：本仓库的 Git 本地配置，不纳入版本控制。
- **关键内容**：
  - `http.sslBackend = openssl`
  - `http.sslCAInfo = <仓库>/.git/win-root-ca.pem`
  - `user.name = BuBaiZhi`、`user.email = BuBaiZhi@users.noreply.github.com`
  - `remote.origin.url = https://github.com/BuBaiZhi/review-record.git`
  - `branch.main.remote = origin`、`branch.main.merge = refs/heads/main`
- **为什么改 TLS 配置**：本机通过代理 `127.0.0.1:4457` 访问外网，代理对 GitHub 做了 TLS 中间人解密，使用一张只安装在 Windows 证书库中的私有 CA 签发证书。默认的 schannel 后端能验通证书链，却卡在**吊销状态检查**（代理环境访问不到 OCSP 服务器），报 `CRYPT_E_NO_REVOCATION_CHECK`；切换到 OpenSSL 后端后又因不信任该私有 CA 而报 `unable to get local issuer certificate`。
- **处理原则**：**没有关闭证书校验**（`sslVerify=false` 会让访问令牌暴露给中间人），而是把 Windows 根证书库导出给 OpenSSL 使用，校验保持完整。
- **规则**：换机器、换网络环境或代理策略变更时，需重新生成本地 CA 文件并更新 `sslCAInfo`。

### 8. `.git/win-root-ca.pem`（2026-09-30 新增）

- **职责**：存放从 Windows 根证书库导出的根证书（`LocalMachine\Root` + `CurrentUser\Root`，共 102 张），供 Git 的 OpenSSL 后端完成证书链校验。
- **生成方式**：
  ```powershell
  Get-ChildItem Cert:\LocalMachine\Root, Cert:\CurrentUser\Root |
    ForEach-Object {
      "-----BEGIN CERTIFICATE-----"
      [Convert]::ToBase64String($_.RawData, 'InsertLineBreaks')
      "-----END CERTIFICATE-----"
    } | Set-Content .git\win-root-ca.pem
  ```
- **规则**：位于 `.git/` 内，**不进入版本控制、不上传 GitHub** —— 它只对本机网络环境有意义。

### 9. `documents/product-requirements.md`（2026-09-30 新增）

- **职责**：产品需求文档（PRD）。只描述"要做什么、为什么这么做"，不写实现细节。
- **关键内容**：产品定位与边界、目标用户与场景、P0/P1/P2 需求清单、关键产品决策及理由、非功能性需求、风险与对策、成功标准、待确认事项。
- **规则**：只增不删。需求变更时在末尾追加新版本小节，不覆盖历史。当前版本 `1.0`。
- **关系**：与 `functional-design.md` 是"需求 ↔ 实现"的上下游关系。需求评审通过后据此写设计。

### 10. `documents/functional-design.md`（2026-09-30 新增）

- **职责**：功能设计文档。描述"怎么做"——模块划分、流程、界面、数据、异常处理。
- **关键内容**：三项技术前提（WebView 不支持语音识别 / 系统识别 60 秒上限 / 分区存储需授权）、技术架构图、信息架构、13 个功能模块、6 条核心流程（含分段续录机制）、界面设计、数据模型与 `.md` 格式、12 项异常与边界、外部接口、5 个里程碑。
- **规则**：只增不删，变更在末尾追加版本小节。当前版本 `1.0`。
- **关系**：`product-requirements.md` 的下游产物；上游约束来自该 PRD。

### 11. `documents/development-plan.md`（2026-09-30 新增）

- **职责**：开发计划方案集。给出多套分阶段实施路径，供评估选择；含每阶段的交付物与验收标准。
- **关键内容**：比较维度定义、三个技术不确定点、4 套方案（风险前置 / 渐进交付 / 体验优先 / 一次交付）的阶段表、横向对比、推荐意见、通用验收方法（验收流程、量化指标、回归清单、7 天最终验收）。
- **规则**：只增不删。方案调整在末尾追加版本小节；**选定方案后在本文件中记录决策，不删除未选方案**。当前版本 `1.0`，状态为待评估。
- **关系**：`functional-design.md` 的下游产物；选定方案后据此出正式实施计划。

---

## 四、技术选型说明

### 为什么用 Capacitor 而不是纯网页

需求包含"语音输入"和"直接写入手机文件夹"。纯网页在这两点上受限：

- 安卓 WebView 不支持 `webkitSpeechRecognition`，网页语音识别在壳内不可用；
- 浏览器沙箱内的数据无法被 Obsidian 这类外部应用直接读取。

Capacitor 提供原生桥接层，可调用安卓的语音识别服务、按绝对路径读写文件，同时保留 Web 技术栈的开发效率。

### 为什么 AI 整理用 DeepSeek

- 接口兼容 OpenAI 格式，调用简单；
- 中文口语转书面语的效果好；
- 支持 JSON 输出模式，便于把结果直接映射到模板栏目；
- 价格低，单次复盘成本约几分钱。

### 为什么存储用 Markdown

`.md` 是纯文本格式，不绑定任何应用。Obsidian、Notion、语雀等均可直接读取。即便本应用将来停止维护，笔记也不会丢失。

### 为什么电脑联动靠"文件夹同步"而非"导入"

Obsidian 的"库"本质上就是一个装着 `.md` 文件的普通文件夹，没有专有数据库。因此让手机应用把文件写进某个文件夹，再让这个文件夹同步到电脑，桌面 Obsidian 打开它就是全部历史 —— 全程不需要"导入"动作。

---

## 五、数据流

```
用户语音
   ↓  手机原生语音识别（免费，无需 Key）
转写文本
   ↓  用户确认 / 直接编辑
原始记录
   ↓  DeepSeek API 按模板整理
结构化四栏内容
   ↓  用户可修改
复盘条目
   ↓  保存
    ├──→ 本地存储（应用内，永不丢失）
    └──→ Obsidian 库文件夹（写入 .md，供同步与桌面端读取）
                ↓
        坚果云 / Syncthing 同步
                ↓
        电脑端 Obsidian 打开同一文件夹
```

---

## 六、待建文件规划

| 计划文件 | 职责 |
| --- | --- |
| `review-app/www/app.js` | 应用全部逻辑：语音识别、DeepSeek 调用、本地存储、列表渲染、编辑、导出 |
| `review-app/package.json` | 依赖声明与构建脚本 |
| `review-app/capacitor.config.json` | Capacitor 配置（应用名、包名、webDir） |
| `review-app/android/` | Capacitor 生成的安卓原生工程 |
| `.gitignore` | 排除 `.android-build/`、`android/` 构建产物等 —— **已于 1.1 创建，见「文件清单」第 6 条** |
| `documents/` 下其他文档 | 按需新增，如使用说明、Obsidian 配置指南 |

---

## 七、变更记录

| 日期 | 版本 | 变化 |
| --- | --- | --- |
| 2026-09-30 | 1.0 | 建立 `documents` 目录；创建 `progress.md` 与 `architecture.md`；记录 `index.html`、`styles.css`、`setup.sh` 三个已存在文件的职责 |
| 2026-09-30 | 1.1 | 新增 `.gitignore` 文件，纳入文件清单（第 6 条），并同步更新目录结构总览与文件类型说明中的仓库排除策略 |
| 2026-09-30 | 1.2 | 文件结构无变化。编译工具链安装完成并实测验证通过（JDK 17、Android SDK `android-34` / `build-tools 34.0.0`、7 项许可全部接受），仅记录状态 |
| 2026-09-30 | 1.3 | 新增 `.git/config`（第 7 条）与 `.git/win-root-ca.pem`（第 8 条）职责说明，含 TLS 故障根因与修复方式；目录结构总览与文件类型说明补充 `.git/` 条目 |
| 2026-09-30 | 1.4 | 文件结构无变化。首次推送成功，远程分支 `main` 建立并与本地同源；`.gitignore` 规划项标记为已完成 |
| 2026-09-30 | 1.5 | 新增 `product-requirements.md`（第 9 条）与 `functional-design.md`（第 10 条）两份产品文档，纳入文件清单与目录结构总览。**此版本为纯文档产出，未改动任何代码** |
| 2026-09-30 | 1.6 | 新增 `development-plan.md`（第 11 条）开发计划方案文档，纳入文件清单与目录结构总览。**纯文档产出，未改动代码** |
