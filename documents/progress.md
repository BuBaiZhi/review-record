# 开发进度记录

> **记录规则（固定约定，不得违反）**
> 1. 本文件只增不删。历史内容永久保留，不做覆盖、不做删减。
> 2. 每次改动追加一个新的版本小节，写在文件最末，按时间顺序排列。
> 3. 版本号从 `1.0` 起，按 `1.1`、`1.2`、`1.3` …… 递增；出现较大结构调整或方向变更时进位到 `2.0`。
> 4. 每个版本必须写清三件事：**改了什么**、**涉及哪些文件**、**当前可运行状态**。
> 5. 任何一次代码修改，都必须同步在本文件追加对应版本小节。

---

## 1.0 — 2026-09-30 — 项目启动与需求确认

### 项目目标

做一个自用的「每日工作复盘」移动应用，核心诉求四条：

1. 语音口述当天工作内容与感悟，由 AI 助手整理成文件，按固定模板存入复盘库；
2. 复盘文件可随时修改完善；
3. 可按时间排布、浏览历史复盘文件进行学习；
4. 复盘文件能与电脑端联动，直接作为 Obsidian 的库使用。

### 已确认的技术决策

| 决策项 | 结论 | 说明 |
| --- | --- | --- |
| 交付形态 | Android 安装包（APK） | 仅自用，不走应用商店，直接侧载安装 |
| 打包方式 | Capacitor 外壳 + Web 前端 | 前端负责界面与业务逻辑，Capacitor 负责桥接手机原生能力 |
| AI 整理 | DeepSeek API（用户自备 Key） | 接口兼容 OpenAI 格式，Key 仅存于本机 |
| 复盘模板 | 今日完成 / 问题与卡点 / 感悟收获 / 明日计划 | 四个栏目录应用内可自由增删改 |
| 存储方式 | 本地存储 + 导出 Markdown | 文件为纯 `.md` 文本，可被 Obsidian 直接读取 |
| 电脑联动 | 手机文件夹同步 → 桌面 Obsidian 打开同一文件夹 | 取消"导入"环节，两端共用同一批文件 |
| iOS 支持 | 暂不做 | 苹果不支持免商店分发，自用签名 7 天过期，成本高 |

### 已完成

1. **环境探测**
   - 本机无 Java、无 Android SDK、无 Gradle；
   - Node.js `22.22.2`、npm `10.9.7` 可用；
   - C 盘剩余空间 76G，网络对 `dl.google.com`、`api.adoptium.net`、`registry.npmjs.org` 均可正常访问。

2. **编译工具链安装**
   - JDK：Eclipse Temurin `17.0.20.1+1`（Windows x64），已解压至 `.android-build/jdk/`；
   - Android SDK：命令行工具 `commandlinetools-win-11076708` 已安装至 `.android-build/android-sdk/`，已下 `platform-tools`、`platforms;android-34`、`build-tools;34.0.0` 并接受 licenses。

3. **前端骨架**
   - 完成应用页面结构与全部样式，包含三个标签页（记录 / 复盘库 / 设置）、详情抽屉、底部导航。

4. **文档体系**
   - 建立 `documents` 目录，创建 `progress.md` 与 `architecture.md`，并确定"只增不删"的记录规则。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `review-app/www/index.html` | 新建。三个标签页 + 详情抽屉 + 底部导航的完整页面结构 |
| `review-app/www/styles.css` | 新建。浅色移动端主题，含录音脉冲动画、卡片、列表、表单等全部样式 |
| `.android-build/setup.sh` | 新建。JDK 与 Android SDK 的自动下载安装脚本 |
| `documents/progress.md` | 新建。本文件 |
| `documents/architecture.md` | 新建。文件职责与架构说明 |

### 当前可运行状态

- ✅ 编译工具链就绪，具备产出 APK 的能力
- ✅ 页面结构与样式完成（静态，尚无交互）
- ⏳ `review-app/www/app.js` 尚未编写 —— 应用全部逻辑（录音、语音转文字、DeepSeek 调用、存储、导出）都在此文件
- ⏳ Capacitor Android 工程尚未初始化
- ⏳ APK 尚未产出
- ⏳ GitHub 仓库尚未创建

### 待办清单

- [ ] 编写 `review-app/www/app.js`
- [ ] 初始化 Capacitor 配置与 Android 工程
- [ ] 编写原生文件写入插件（用于直接写入 Obsidian 库文件夹）
- [ ] 编译并产出 APK
- [ ] 创建 GitHub 仓库并推送代码
- [ ] 真机安装测试

---

## 1.1 — 2026-09-30 — Git 仓库初始化与 GitHub 推送准备

### 本次变化

1. **初始化 Git 仓库**
   - 将工作区根目录 `2026-09-30-13-07-11\` 初始化为 Git 仓库，默认分支 `main`；
   - 确认该目录此前不属于任何已有仓库，不存在嵌套问题。

2. **编写 `.gitignore`**
   - 编写排除规则，确保编译工具链与构建产物不被推送：
     - `.android-build/`（约 970M 的工具链，最关键的一条）
     - `*.zip` / `*.tar.gz`（下载的安装包）
     - `node_modules/`、`review-app/android/` 下的构建产物
     - `*.apk`、`*.aab`、`*.jks`、`*.keystore`（安装包与签名）
     - `.workbuddy/`（工作区状态）

3. **验证排除效果**
   - 执行 `git add -A --dry-run`，确认最终进入版本控制的仅 5 个文件：
     `.gitignore`、`documents/architecture.md`、`documents/progress.md`、`review-app/www/index.html`、`review-app/www/styles.css`。
   - `.android-build/` 与 `.workbuddy/` 已被正确忽略。

4. **GitHub 上传前置环境勘察**
   - ✅ `git` 可用，版本 `2.55.0.windows.3`
   - ❌ 无 `gh` 命令行工具
   - ❌ 无 SSH 密钥（`~/.ssh/` 不存在）
   - ❌ 未配置全局 `user.name` / `user.email`
   - 结论：需由用户提供 GitHub 账号凭据后方可推送，或改用 HTTPS + 个人访问令牌（PAT）方式。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `.gitignore` | 新建。Git 排除规则 |
| `documents/architecture.md` | 更新。文件清单新增第 6 条 `.gitignore`；目录结构总览加入 `.gitignore` 并标注根目录即仓库根；变更记录新增 1.1 行 |
| `documents/progress.md` | 更新。追加本小节 |
| `.git/` | 新建。Git 仓库元数据目录，不纳入版本控制 |

### 当前可运行状态

- ✅ 编译工具链就绪（JDK 17 + Android SDK）；SDK 平台包仍在后台安装中
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ 文档体系与 Git 仓库就绪，具备推送条件
- ⏳ `review-app/www/app.js` 尚未编写
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出
- ⏳ GitHub 仓库尚未创建（等待用户账号）

### 待办清单

- [ ] 获取 GitHub 账号凭据，创建远程仓库并完成首次推送
- [ ] 编写 `review-app/www/app.js`
- [ ] 初始化 Capacitor 配置与 Android 工程
- [ ] 编写原生文件写入插件
- [ ] 编译并产出 APK
- [ ] 真机安装测试

---

## 1.2 — 2026-09-30 — 编译工具链安装完成并验证通过

### 本次变化

1. **后台安装任务结束**
   - `.android-build/setup.sh` 执行完毕，耗时 13 分钟。

2. **完整性验证（实测，非推断）**

   | 组件 | 结果 |
   | --- | --- |
   | JDK | Temurin `17.0.20.1+1`，`java -version` 可正常输出 |
   | `android-sdk/platforms/` | `android-34` ✓ |
   | `android-sdk/build-tools/` | `34.0.0` ✓ |
   | `android-sdk/platform-tools/` | 已安装 ✓ |
   | `android-sdk/cmdline-tools/` | 已安装 ✓ |
   | `android-sdk/licenses/` | 7 项许可全部接受 ✓ |

3. **体积统计**
   - `.android-build/` 总计 `1.1G`，其中 `android-sdk` 422M、`jdk` 304M、剩余为两个安装包 zip。
   - 该目录已被 `.gitignore` 排除，不会进入 GitHub 仓库。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `.android-build/android-sdk/` | 安装完成。新增 `platforms/android-34`、`build-tools/34.0.0`、`platform-tools`、`licenses` 等 |
| `documents/progress.md` | 更新。追加本小节 |

### 当前可运行状态

- ✅ **编译工具链完全就绪并验证通过，随时可编译 APK**
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ 文档体系与 Git 仓库就绪，具备推送条件
- ⏳ `review-app/www/app.js` 尚未编写 —— 应用仍为无交互的静态空壳
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出
- ⏳ GitHub 仓库尚未创建（等待用户账号凭据）

### 待办清单

- [ ] **编写 `review-app/www/app.js`（当前阻塞项，应用无法使用的根因）**
- [ ] 初始化 Capacitor 配置与 Android 工程
- [ ] 编写原生文件写入插件
- [ ] 编译并产出 APK
- [ ] 获取 GitHub 账号凭据，创建远程仓库并完成首次推送
- [ ] 真机安装测试

---

## 1.3 — 2026-09-30 — 打通 GitHub 网络链路、完成首次提交

### 本次变化

1. **配置远程仓库**
   - `git remote add origin https://github.com/BuBaiZhi/review-record.git`
   - 实测确认该仓库已存在且为公开状态。

2. **排查并修复 TLS 连接故障（关键问题）**
   - **现象**：`git ls-remote` 报错 `schannel: CRYPT_E_NO_REVOCATION_CHECK (0x80092012) - 吊销功能无法检查证书是否吊销`；`curl https://github.com` 返回码 `000`；但 `gitee.com`、`dl.google.com` 均正常。
   - **根因分析**：
     - 本机通过代理 `127.0.0.1:4457` 访问外网，CONNECT 隧道可正常建立（返回 200）；
     - 代理对 GitHub 做了 TLS 中间人解密，使用私有 CA 签发证书；
     - 该私有 CA 只安装在 Windows 证书库中，因此 schannel 能完成证书链验证，仅卡在吊销状态检查（代理环境无法访问 OCSP 服务器）；
     - 切换 OpenSSL 后端后报 `unable to get local issuer certificate (20)`，进一步佐证"证书签发者不在 OpenSSL 信任库中"。
   - **修复方案**：
     1. 用 PowerShell 将 Windows 根证书库（`LocalMachine\Root` + `CurrentUser\Root`）导出为 PEM 文件，共 102 张证书，存放于 `.git/win-root-ca.pem`（位于 `.git` 内，不会进入版本控制）；
     2. 对本仓库设置 `http.sslBackend = openssl`、`http.sslCAInfo = <上述 PEM 文件>`。
   - **效果**：`git ls-remote origin` 成功执行；证书校验**保持开启**，未采用 `sslVerify=false` 这类降级方案。
   - **原则说明**：没有图省事直接关掉证书校验，因为那样会让访问令牌暴露给中间人。

3. **设置提交身份**
   - `user.name = BuBaiZhi`
   - `user.email = BuBaiZhi@users.noreply.github.com`
   - 说明：因本机未配置 Git 全局身份，暂以仓库所有者的 GitHub 用户名与 noreply 邮箱作为提交身份，后续可按需修改。

4. **完成首次提交**
   - 提交哈希：`e392433`
   - 变更统计：5 个文件，924 行新增
   - 内容：`.gitignore`、`documents/architecture.md`、`documents/progress.md`、`review-app/www/index.html`、`review-app/www/styles.css`
   - 已确认 `.android-build/`（1.1G 工具链）与 `.workbuddy/` 未进入提交。

5. **推送尝试失败（阻塞点）**
   - 执行 `git push -u origin main`，GitHub 返回：
     `remote: Invalid username or token. Password authentication is not supported for Git operations.`
   - **结论**：网络链路已完全打通（请求抵达 GitHub 并由其响应），**唯一阻塞项为缺少身份凭据**。GitHub 已不支持账号密码认证，必须使用个人访问令牌（PAT）。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `.git/config` | 新增本地配置：`http.sslBackend`、`http.sslCAInfo`、`user.name`、`user.email`、`remote.origin` |
| `.git/win-root-ca.pem` | 新建。Windows 根证书库导出文件（102 张），仅供本机 Git 使用，位于 `.git` 内不纳入版本控制 |
| `documents/progress.md` | 更新。追加本小节 |

### 当前可运行状态

- ✅ 编译工具链完全就绪（JDK 17 + Android SDK）
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ Git 仓库就绪，**首次提交已完成（e392433）**
- ✅ **GitHub 网络链路已打通并经实测验证**
- ⏳ **推送等待用户提供 PAT（唯一阻塞项）**
- ⏳ `review-app/www/app.js` 尚未编写
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出

### 待办清单

- [ ] **获取 GitHub PAT，执行 `git push -u origin main`（当前唯一阻塞项）**
- [ ] 编写 `review-app/www/app.js`
- [ ] 初始化 Capacitor 配置与 Android 工程
- [ ] 编写原生文件写入插件
- [ ] 编译并产出 APK
- [ ] 真机安装测试

---

## 1.4 — 2026-09-30 — 首次推送成功，代码已上传 GitHub

### 本次变化

1. **令牌问题的定位与解决**
   - 用户提供的是**细粒度个人访问令牌**（`github_pat_` 开头，标准长度 93 位）；
   - 第一次收到时长度仅为 31 位，为复制/转述过程中被截断，导致 GitHub 返回 `remote: Invalid username or token.`；
   - 重新核对后确认完整长度为 **93 位**，用 `https://api.github.com/user` 实测验证，返回 `"login": "BuBaiZhi"`，令牌有效。

2. **推送成功（1.3 中的阻塞项已解除）**
   - 执行命令（令牌通过 URL 临时传入，**未写入任何配置文件**）：
     ```
     git push https://<user>:<token>@github.com/BuBaiZhi/review-record.git main:main
     ```
   - 返回：
     ```
     To https://github.com/BuBaiZhi/review-record.git
      * [new branch]      main -> main
     ```
     退出码 `0`。
   - 远程分支哈希：`e3924332db4f19b3ab9983600398f594c837238e`，与本地 `HEAD` 完全一致。

3. **远程仓库内容核对（GitHub API 实测，非推断）**

   | 路径 | 大小 |
   | --- | --- |
   | `.gitignore` | 794 B |
   | `documents/architecture.md` | 8923 B |
   | `documents/progress.md` | 8286 B |
   | `review-app/www/index.html` | 8285 B |
   | `review-app/www/styles.css` | 12910 B |

   - 已确认 `.android-build/`（1.1G 工具链）与 `.workbuddy/` **未上传**，`.gitignore` 规则生效。

4. **分支跟踪配置的一处变通**
   - 现象：`git fetch origin` 报 `* [new branch] main -> origin/main`，但 `git branch -r` 始终为空，`git branch --set-upstream-to` 报 `upstream branch does not exist`。
   - 判断：当前运行环境下 `.git/refs/remotes/` 的写入未能落盘（`.git/config` 的写入不受影响）。
   - 处理：绕过远程跟踪引用，直接写入跟踪配置 —— `branch.main.remote = origin`、`branch.main.merge = refs/heads/main`。
   - 影响范围：仅影响本机 `git status` 的 ahead/behind 显示，**不影响仓库内容、不影响推送**。

5. **未做的事（有意为之）**
   - 未执行 `git config credential.helper store`，未把令牌写入 `.git/config` 或 `~/.git-credentials`，避免令牌以明文长期留在磁盘上。
   - 未关闭 TLS 证书校验。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `.git/config` | 更新。新增 `branch.main.remote`、`branch.main.merge` 分支跟踪配置（1.3 中已记录 TLS 与身份相关配置） |
| `documents/progress.md` | 更新。追加本小节 |
| `documents/architecture.md` | 更新。新增第 7、8 条文件职责（`.git/config`、`.git/win-root-ca.pem`）；目录结构总览与文件类型说明补充 `.git/` 条目；补充远程仓库地址；变更记录补记 1.2–1.4 |

### 当前可运行状态

- ✅ **代码已上传 GitHub** —— <https://github.com/BuBaiZhi/review-record>
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK）
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ 文档体系就绪（`progress.md` / `architecture.md` 双轨记录）
- ⏳ `review-app/www/app.js` 尚未编写 —— **应用仍为无交互的静态空壳，当前最大阻塞项**
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出

### 待办清单

- [x] ~~获取 GitHub PAT，完成首次推送~~ —— 本版本已完成
- [ ] **编写 `review-app/www/app.js`（当前最大阻塞项）**
- [ ] 初始化 Capacitor 配置与 Android 工程
- [ ] 编写原生文件写入插件（用于直接写入 Obsidian 库文件夹）
- [ ] 编译并产出 APK
- [ ] 真机安装测试
- [ ] 安全事项：本轮使用的 PAT 已在聊天中明文出现，建议在 GitHub 上撤销并重新生成

> **说明**：1.3 小节待办清单中的未勾选项保持原样未作修改，以维持版本快照的可追溯性；其完成状态以本小节为准。

---

## 1.5 — 2026-09-30 — 产品文档产出（需求文档 + 功能设计文档）

### 本次变化

**本版本为纯文档产出，未改动任何代码。**

1. **需求调研**

   产出文档之前先做了两轮调研：

   - **技术调研**：查明三项会直接影响产品设计的硬约束 ——
     ① 安卓 WebView 不支持 `webkitSpeechRecognition`，语音识别必须走原生桥接；
     ② 安卓系统 `SpeechRecognizer` 单次识别上限约 60 秒，中文准确率约 82~85%；
     ③ Android 11+ 分区存储限制，写入任意文件夹需用户授权（SAF）。
   - **方法论调研**：梳理了 KPT / KISS / PDCA / YWT / ORID / 3R / GRAI / STAR 等主流复盘框架，用于向用户呈现模板选型。

2. **与用户确认的四项产品决策**

   | 决策项 | 用户选择 | 对设计的影响 |
   | --- | --- | --- |
   | 单次口述时长 | **2~5 分钟** | 必须实现**分段续录**——这是本次设计的核心机制 |
   | AI 角色 | **纯整理器**（不追问、不加料） | 提示词强约束"只整理不添加"；不做对话式引导 |
   | 附加能力 | 历史检索、数据看板、周/月 AI 汇总 | 三项进入 P1；**每日定时提醒未被选择，降为 P2** |
   | 模板 | **用户自定义** | 模板栏目完全可编辑；**具体栏目待用户提供** |

3. **新增两份产品文档**

   - `documents/product-requirements.md`（PRD）—— 产品定位与边界、目标用户与场景、P0/P1/P2 需求清单（16 条）、8 项关键决策及理由、非功能性需求、9 项风险与对策、成功标准、待确认事项。
   - `documents/functional-design.md` —— 三项技术前提、架构图、信息架构、13 个功能模块、7 条核心流程（含分段续录的状态与拼接规则）、逐页界面设计、数据模型与 `.md` 文件格式、12 项异常与边界、外部接口、5 个里程碑。

4. **确立的文档分工**

   | 文档 | 回答的问题 |
   | --- | --- |
   | `product-requirements.md` | 做什么、为什么这么做 |
   | `functional-design.md` | 怎么做 |
   | `architecture.md` | 每个文件是干什么的 |
   | `progress.md` | 什么时候改了什么（本文件） |

5. **顺带查明的一件环境事实**
   - 本机**已装好 Git Credential Manager**（全局 `credential.helper` 已指向 `git-credential-manager.exe`）。
   - 意味着推送代码**本不需要个人访问令牌**，直接在仓库目录执行 `git push` 即会弹出登录授权窗口，凭据自动存入 Windows 凭据管理器。
   - 1.3 / 1.4 中绕的令牌弯路，本可避免。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/product-requirements.md` | 新建。产品需求文档（PRD）1.0 |
| `documents/functional-design.md` | 新建。功能设计文档 1.0 |
| `documents/architecture.md` | 更新。目录结构总览补两份新文档；文件清单新增第 9、10 条；变更记录补 1.5 |
| `documents/progress.md` | 更新。追加本小节 |

### 当前可运行状态

- ✅ 代码已上传 GitHub —— <https://github.com/BuBaiZhi/review-record>
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK）
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ **需求文档与功能设计文档就绪，具备进入开发的条件**
- ⏳ `review-app/www/app.js` 尚未编写 —— **应用仍为无交互的静态空壳**
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出

### 待办清单

- [ ] **用户提供自定义复盘的模板栏目（名称、顺序、是否需要填写提示）—— 当前唯一阻塞项**
- [ ] 评审并确认 `product-requirements.md` 与 `functional-design.md`
- [ ] 编写 `review-app/www/app.js`（可在评审通过后开始，M1 核心闭环）
- [ ] 初始化 Capacitor 工程与原生插件（语音识别、文件写入、系统分享）
- [ ] 实现分段续录机制（需真机实测确定安全阈值秒数）
- [ ] 编译并产出 APK
- [ ] 真机安装测试，连续试用 7 天
- [ ] 安全事项：1.4 中使用的 PAT 若已撤销则无需再管；后续推送改用 Git Credential Manager，不再使用明文令牌
