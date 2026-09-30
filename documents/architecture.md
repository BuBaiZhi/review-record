# 项目架构说明

> **记录规则（固定约定，不得违反）**
> 1. 每当**新增文件**、**删除文件**或**改变文件职责**时，必须同步更新本文件。
> 2. 每个文件需记录：路径、职责、关键内容、与其他文件的关系。
> 3. 本文件同样只增不删。文件职责发生迁移时，在「变更记录」中追加说明，不抹掉历史描述。

---

## 一、目录结构总览

```
review-record\                        项目根目录 = 工作区根目录（也是 Git 仓库根目录）
                                      完整路径：D:\Projects\review-record\（2026-09-30 由 C 盘迁入，见 progress.md 1.11）
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
│   └── development-plan.md            开发计划：4 套方案对比（决策记录）+ 正式实施计划（2.0 起）
│
├── dist\                              交付产物（**规划中，尚未创建**）：供拷贝到手机的 APK
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
| `dist/` | 否 | ❌ 否 | 交付给手机的 APK（二进制），版本与提交号对应关系记在 `progress.md` |

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
  - 排除 `dist/`（交付产物目录，1.8 新增）—— 与 `*.apk` 规则叠加，确保整个交付目录都不入库
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
- **规则**：只增不删。需求变更时在末尾追加新版本小节，不覆盖历史。当前版本 `1.1`（1.12 补记：语音链路切换对需求、风险与非功能性需求的影响）。
- **关系**：与 `functional-design.md` 是"需求 ↔ 实现"的上下游关系。需求评审通过后据此写设计。

### 10. `documents/functional-design.md`（2026-09-30 新增）

- **职责**：功能设计文档。描述"怎么做"——模块划分、流程、界面、数据、异常处理。
- **关键内容**：三项技术前提（WebView 不支持语音识别 / 系统识别 60 秒上限 / 分区存储需授权）、技术架构图、信息架构、13 个功能模块、6 条核心流程（含分段续录机制）、界面设计、数据模型与 `.md` 格式、12 项异常与边界、外部接口、5 个里程碑；**1.1 新增第十三节「语音识别可用性风险」**（国内 OEM ROM 的 `isRecognitionAvailable()` 陷阱、正确适配方式、adb 实测步骤、云端 ASR 备选）、第十一节补记已定稿的模板栏目。
- **规则**：只增不删，变更在末尾追加版本小节。当前版本 `1.3`（**1.3 起语音链路改为云端 ASR，新增第十五节为当前有效设计**）。
- **关系**：`product-requirements.md` 的下游产物；上游约束来自该 PRD。

### 11. `documents/development-plan.md`（2026-09-30 新增）

- **职责**：开发计划文档。1.0 为多套方案的对比与评估；**2.0 起兼作正式实施计划**（第八节）。
- **关键内容**：比较维度定义、三个技术不确定点、4 套方案（风险前置 / 渐进交付 / 体验优先 / 一次交付）的阶段表、横向对比、推荐意见、通用验收方法（验收流程、量化指标、回归清单、7 天最终验收）；第八节为选定后的正式实施计划（1 个探针包 + 4 个可用版本，逐阶段的执行步骤 / 用户动作 / 交付方式 / 验收清单）。
- **规则**：只增不删。方案调整在末尾追加版本小节；**选定方案后在本文件中记录决策，不删除未选方案**。当前版本 `2.2`（语音链路切换后，阶段一与 v0.0 探针定义已同步重写），状态为**已选定方案二（渐进交付）**，已进入阶段一。
- **关系**：`functional-design.md` 的下游产物；第八节即据此细化的正式实施计划。

---

## 四、技术选型说明

### 为什么用 Capacitor 而不是纯网页

需求包含"语音输入"和"直接写入手机文件夹"。纯网页在这两点上受限：

- 安卓 WebView 不支持 `webkitSpeechRecognition`，网页语音识别在壳内不可用；
- 浏览器沙箱内的数据无法被 Obsidian 这类外部应用直接读取。

Capacitor 提供原生桥接层，可调用安卓的语音识别服务、按绝对路径读写文件，同时保留 Web 技术栈的开发效率。

> **1.12 修订**：语音识别**已不再**是使用 Capacitor 的理由 —— 语音链路已改为「WebView 本机录音 + 上传云端 ASR」（`functional-design.md` 第十五节），纯 Web 即可实现。
>
> **Capacitor 仍然必需，理由收窄为两条**：① **按绝对路径写入指定文件夹**（Obsidian 库，含分区存储授权 SAF），浏览器沙箱做不到；② 提供可安装的 APK 外壳。换言之，**要原生能力的原因从"语音"变成了"文件"**。

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

> **1.12 修订**：下面这段数据流的**第一段已变更** —— 现在是「**WebView `MediaRecorder` 录音（本机）→ 上传云端 ASR 转写（硅基流动 SenseVoiceSmall，免费）→ 转写文本**」。录音环节离线可用，未联网时音频暂存本机、联网后可补转写。完整修订后的链路图见 `functional-design.md` 第十五节 15.2。
>
> 原图按只增不删规则保留如下，**除第一段外的其余环节（DeepSeek 整理 → 保存 → Obsidian 镜像 → 同步 → 桌面端）全部不变**。

```
用户语音
   ↓  手机原生语音识别（免费，无需 Key）      ← 1.12 起已变更为「本机录音 + 上传云端 ASR」
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
| `review-app/www/app.js` | 应用全部逻辑：**录音与上传转写**、DeepSeek 调用、本地存储、列表渲染、编辑、导出（**1.12 修订**：原写为"语音识别"，已改；语音部分变为纯 Web 代码，不含原生语音插件） |
| `review-app/package.json` | 依赖声明与构建脚本 |
| `review-app/capacitor.config.json` | Capacitor 配置（应用名、包名、webDir） |
| `review-app/android/` | Capacitor 生成的安卓原生工程 |
| `.gitignore` | 排除 `.android-build/`、`android/` 构建产物、`dist/` 等 —— **已于 1.1 创建，见「文件清单」第 6 条；1.8 补充 `dist/` 排除项** |
| `dist/` | 交付产物目录，存放拷贝到手机的 APK（`review-record-v0.1.apk` 等）。**规划中，首次打包时创建** |
| `documents/` 下其他文档 | **自 1.8 起不再自行新增**；除 `progress.md` 与 `architecture.md` 外，新增文档须先经用户同意 |

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
| 2026-09-30 | 1.7 | 文件结构无变化。补推 1.5 / 1.6 两个提交成功，本地与远程 `main` 同步至 `002a4b5`。记录一处**环境事实**：本机 git 为 PortableGit 便携版、未加入 PATH（位于 `C:\Users\20472\.workbuddy\binaries\PortableGit\versions\1.2.0\cmd\git.exe`），且已由 Git Credential Manager 2.9.0 承载凭据，后续推送无需令牌 |
| 2026-09-30 | 1.8 | **文件结构无变化**（`dist/` 尚为规划项）。用户选定方案二：`development-plan.md` 升级至 2.0 并兼作正式实施计划，第 11 条职责与状态同步更新；`.gitignore` 新增 `dist/` 排除项并更新第 6 条说明；「待建文件规划」新增 `dist/` 行，并把「documents 下其他文档」改为**须经用户同意方可新增** |
| 2026-09-30 | 1.9 | **文件结构无变化**。用户提供手机型号（OPPO Reno14 / ColorOS 15）与模板栏目，据此查出一处**影响语音方案可行性的风险**：国内 OEM ROM 上 `SpeechRecognizer.isRecognitionAvailable()` 返回 `false`，需读 `Settings.Secure.voice_recognition_service` 定向调用 OEM 引擎，且 `targetSdk ≥ 31` 须声明 Manifest `<queries>`。第 10 条 `functional-design.md` 更新至 1.1（新增第十三节），第 11 条 `development-plan.md` 更新至 2.1（阶段一新增 adb 前置探测）。**未改动代码** |
| 2026-09-30 | 1.10 | **文件结构无变化**。① 模板栏目第 4 项经用户确认由「明日代办」修正为**「明日待办」**（`functional-design.md` 第十一节、`development-plan.md` 8.13）。② `functional-design.md` 更新至 **1.2**：第十三节新增 13.5~13.8，评估**云端 ASR 方案的实现难度**（技术难度低于系统识别，原生代码量降至 0，分段续录机制可整体删除；`FunAudioLLM/SenseVoiceSmall` 经核实免费，但实名认证为前置）、两个待实测未知数（录音格式兼容性、单文件时长上限）、验证成本（可全程在电脑完成）。③ 主路线是否切换**待用户拍板**，未擅自改动 5.2 等设计。**未改动代码** |
| 2026-09-30 | 1.11 | **项目根目录由 C 盘迁移至 `D:\Projects\review-record\`**，文件结构本身无变化（`documents/`、`review-app/`、`.android-build/` 的组成与职责完全一致）。迁移前完成路径依赖排查，三处硬编码**均已改为新路径**：`.git/config` 的 `http.sslCAInfo`、`.android-build/setup.sh` 的 `TOOLS`、`.workbuddy/memory/MEMORY.md` 的工作区根目录。Android SDK 与 JDK 内部经确认无绝对路径，工具链保持可移植。迁移采用**复制而非剪切**，并跳过 `jdk17.zip`、`cmdline-tools.zip`、`cmdtools/` 共 329M 无用文件。**C 盘源目录完整保留未动，作为回退点。未改动代码** |
| 2026-09-30 | 1.12 | **文件结构无变化**（`dist/` 仍为规划项，未创建）。**语音链路主路线切换为云端 ASR**（用户就 `functional-design.md` 13.8 三问答复「都可以」）。三份文档同步修订：`functional-design.md` → **1.3**（新增第十五节为当前有效设计，前文冲突小节打废弃标记、原文保留）、`development-plan.md` → **2.2**（8.1 / 8.4 / 8.5 / 8.12 / 8.13 同步修订，阶段一不再写原生语音代码）、`product-requirements.md` → **1.1**（新增补记，修正风险 2/3、新增三条风险、修正非功能性需求）。本文件同步更新：**第四节的 Capacitor 选型理由收窄为"文件写入 + APK 外壳"两条**、**第五节数据流第一段加变更标记**、**第六节 `app.js` 职责由"语音识别"改为"录音与上传转写"**、文件清单第 9/10/11 条的当前版本号更新。**未改动任何代码** |
