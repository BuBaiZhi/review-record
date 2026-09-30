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

---

## 1.6 — 2026-09-30 — 开发计划方案产出（待评估）

### 本次变化

**本版本为纯文档产出，未改动任何代码。**

1. **新增 `documents/development-plan.md`**，给出 4 套开发计划方案供评估选择。

2. **方案设计的前提判断**

   梳理出本项目三个技术不确定点，方案的差别本质上就是"什么时候去碰这三件事"：

   | # | 不确定点 | 危险程度 |
   | --- | --- | --- |
   | 1 | 原生语音识别 + 分段续录能否真机跑通 | **唯一"可能根本做不成"的事**，需自写安卓原生插件 |
   | 2 | 写入 Obsidian 库文件夹是否顺畅 | 权限模型不对会反复弹窗或写不进去 |
   | 3 | AI 按自定义模板整理的效果够不够好 | 做得到但可能做得不好，需反复调提示词 |

3. **四套方案**

   | 方案 | 思路 | 阶段数 | 首次可用 | 交付次数 |
   | --- | --- | --- | --- | --- |
   | 一 风险前置 | 先用两阶段试掉技术不确定点，再正式开发 | 3 | 晚 | 3 |
   | 二 渐进交付 | 每阶段结束都给一个真能用的 APK | 4 | **最早** | 4 |
   | 三 体验优先 | 先在电脑浏览器调好交互与 AI，再上手机 | 4 | 中 | 4 |
   | 四 一次交付 | 全量开发完再打包 | 2 | 最晚 | 1 |

4. **给出的建议**
   - **推荐方案二（渐进交付）**，并把方案一的"风险前置"思想吸收进阶段 1 —— 阶段 1 开工第一件事不是写界面，而是用最小代价先验证"原生语音识别 + 分段续录"能否真机跑通。
   - **不推荐方案四**：中途无法纠偏，三个技术风险全部推迟到最后暴露，返工量最大。
   - 方案三仅在"非常在意 AI 整理文字质量"时值得选，代价是手机语音体验要晚一个阶段才验证。

5. **补充了通用验收方法**（回答"如何检验交付结果"）
   - 验收流程：交付 APK → 按清单逐项操作 → 不合格项修复后重验 → **不通过不进入下一阶段**
   - 合格验收标准的三个条件：能在手机上操作出来、结果客观可判定、写明临界条件
   - 全程量化底线指标：冷启动 ≤2 秒、进入录音 ≤2 次点击、AI 整理 ≤30 秒、单次费用 <0.1 元、APK <15MB、断网时录音与保存可用、任何失败路径不丢数据
   - 回归清单：每进入新阶段要重跑上一阶段全部检查项
   - **最终验收：连续 7 天真实使用** —— 唯一真正的验收标准

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/development-plan.md` | 新建。开发计划方案文档 1.0，状态为待评估 |
| `documents/architecture.md` | 更新。目录结构总览补本文件；文件清单新增第 11 条；变更记录补 1.6 |
| `documents/progress.md` | 更新。追加本小节 |

### 当前可运行状态

- ✅ 代码已上传 GitHub —— <https://github.com/BuBaiZhi/review-record>
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK）
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ 需求文档、功能设计文档、开发计划方案文档就绪
- ⏳ `review-app/www/app.js` 尚未编写 —— **应用仍为无交互的静态空壳**
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出

### 待办清单

- [ ] **用户评估并选定开发计划方案 —— 当前唯一阻塞项**
- [ ] 用户提供自定义复盘的模板栏目（阻塞阶段 2 的 AI 提示词定稿）
- [ ] 按选定方案出正式实施计划
- [ ] 编写 `review-app/www/app.js` 与原生插件
- [ ] 编译并产出 APK
- [ ] 真机安装测试 → 各阶段验收 → 最终连续 7 天试用

> **补充说明（同一时点补记）**
> 本版本改动已本地提交（`a829e2c`），但**推送未成功** —— 1.4 小节使用的个人访问令牌已被撤销，GitHub 返回 `remote: Invalid username or token.`。
> 这属于预期内的安全动作，不是故障。待凭据配置好后执行一次 `git push` 即可同步；后续推送统一改用 Git Credential Manager（本机已装），不再使用明文令牌。
> 因此：**1.6 的改动目前只在本机，尚未出现在 GitHub 上。**

---

## 1.7 — 2026-09-30 — 补推 1.5 / 1.6 提交成功，Git 凭据链路打通

### 本版本做了什么

1. **排查 `git` 命令在 PowerShell 中不可用的问题**
   - 现象：用户在 PowerShell 中执行 `git push`，报错 `无法将"git"项识别为 cmdlet、函数、脚本文件或可运行程序的名称`
   - 原因：本机的 git 并非常规安装，而是 **PortableGit 便携版**，未加入系统 PATH。其真实路径为：
     `C:\Users\20472\.workbuddy\binaries\PortableGit\versions\1.2.0\cmd\git.exe`
   - 说明：该路径属于 WorkBuddy 管理的**隔离运行时目录**（与 Python、Node 同体系），不属于用户常规安装的软件，因此不会自动进入 PATH。这属于环境特性，不是故障。

2. **使用全路径 git 执行推送，一次成功**
   - 待推送提交两个：`a829e2c`（1.6 开发计划方案）、`002a4b5`（1.6 推送状态补记）
   - 推送结果：`4888741..002a4b5  main -> main`，退出码 `0`
   - 远程 `refs/heads/main` 已独立确认为 `002a4b503b064b0ed06edbfa631ca4395b5f68f2`，与本地 HEAD 一致

3. **凭据链路说明（后续判断依据，重要）**
   - 本机已配置 **Git Credential Manager 2.9.0** 作为全局 `credential.helper`
   - 本次推送**未要求输入任何令牌**，说明 GCM 已取得可用的 GitHub 凭据并在请求中复用
   - 仓库 `remote` 地址中**不含令牌**：`https://github.com/BuBaiZhi/review-record.git`
   - 磁盘上**不存在明文 `.git-credentials` 文件**
   - 结论：**此后推送不再需要个人访问令牌，也不再需要用户在对话中提供任何凭据。**

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/progress.md` | 更新。追加本小节 |
| `documents/architecture.md` | 更新。变更记录补 1.7；文件结构无变化 |
| 其他 | 无。**本版本未改动任何代码** |

### 当前可运行状态

- ✅ 本地与 GitHub 远程完全同步（`main` = `002a4b5`）
- ✅ Git 凭据链路打通，后续推送无需令牌
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK 34 + build-tools 34.0.0）
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ 需求文档、功能设计文档、开发计划方案文档就绪
- ⏳ `review-app/www/app.js` 尚未编写 —— **应用仍为无交互的静态空壳**
- ⏳ Capacitor 工程尚未初始化
- ⏳ APK 尚未产出

### 待办清单

- [ ] **用户评估并选定开发计划方案 —— 当前唯一阻塞项**
- [ ] 用户提供自定义复盘的模板栏目（阻塞阶段 2 的 AI 提示词定稿）
- [ ] 按选定方案出正式实施计划
- [ ] 编写 `review-app/www/app.js` 与原生插件
- [ ] 编译并产出 APK
- [ ] 真机安装测试 → 各阶段验收 → 最终连续 7 天试用

> **备注**：1.6 小节末尾关于「推送未成功」的补充说明，是本版本之前的**真实状态快照**，按「只增不删」规则保留不作修改；该状态已由本小节解除。

---

## 1.8 — 2026-09-30 — 用户选定方案二，开发计划细化为正式实施计划

### 本版本做了什么

1. **用户完成方案评估，选定「方案二 · 渐进交付」**
   - 同时确认把「原生语音识别 + 分段续录」的验证提前到最前（采纳 1.6 小节中给出的建议）

2. **`documents/development-plan.md` 由 1.0 升级为 2.0，新增第八节「正式实施计划」**
   - 把方案二细化为 **1 个探针包 + 4 个可用版本**：
     `v0.0 语音验证包` → `v0.1 能记录` → `v0.2 AI 整理` → `v0.3 通电脑` → `v1.0 好回看`
   - **每个阶段都写清了四件事**：我按顺序执行的步骤表、需要用户做的事、交付方式、可勾选的验收清单
   - 新增配套小节：交付机制（APK 如何到手机）、开工前需要用户提供的三项、每阶段固定动作、全局量化底线引用、变更与回滚、7 条默认决定、当前待用户拍板事项
   - **明确 `v0.0` 是一次刻意增加的极小交付** —— 用最小的包去试全项目唯一"可能根本做不成"的风险
   - 明确下一步**换方案的唯一触发点**：`v0.0` 的真机语音验证不通过（备选为第三方语音服务，附带注册、付费、包体增大三条代价）
   - 第一～七节作为决策记录**原文保留未改**，仅在「文档信息」表下与第七节末尾各追加一行指向新内容的说明

3. **`documents/` 目录权限收窄（用户新要求）**
   - 自本版本起，**除 `progress.md` 与 `architecture.md` 外，不再自行新建任何文档**
   - 需要新文档时先征求用户同意
   - 因此本次细化**写入已有的 `development-plan.md`，未新建文件**

4. **`.gitignore` 新增 `dist/` 排除项**
   - `dist/` 将用于存放交付给手机的 APK
   - APK 属二进制产物，不入版本库；版本与 git 提交号的对应关系记录在本文件中
   - 注：`.gitignore` 原有的 `*.apk` 规则已能排除 APK，新增 `dist/` 是为了把该目录整体排除，避免混入其他中间文件

5. **修复一处 git 推送永久卡死故障（重要，环境级）**
   - **现象**：`git push` 不报错、不退出，长时间无任何输出（实测超过 3 分钟无响应）
   - **排查过程**：网络侧排除完毕 —— `curl --ssl-no-revoke https://github.com` 与仓库 `info/refs` 接口均秒回 `200`；`git ls-remote` 亦正常。随后检查进程，发现一个 `git-credential-helper-selector.exe` 常驻
   - **根因**：Git 的 `credential.helper` 是**累加**配置，不是覆盖
     | 层级 | 配置来源 | 值 |
     | --- | --- | --- |
     | 系统级 | `PortableGit/…/etc/gitconfig`（PortableGit 出厂默认） | `helper-selector` |
     | 全局级 | `C:/Users/20472/.gitconfig` | `!"…git-credential-manager.exe"` |
     | 结果 | 两个助手都会被调用；`helper-selector` 会弹出**选择凭据助手的 GUI 对话框**，在后台/无界面运行时该窗口不可见，进程永久等待点击 | → 推送卡死 |
   - **验证凭据本身是好的**：`git credential fill`（`GCM_INTERACTIVE=never`）正常返回 `username=BuBaiZhi` 与密码，说明 Windows 凭据管理器中**早已存在可用凭据**，问题与凭据无关
   - **修复方式**：
     1. 备份原全局配置到 `~/.gitconfig.bak-20260930`
     2. `git config --global --replace-all credential.helper ""` —— 置空值用于**清空先前累加的助手列表**
     3. `git config --global --add credential.helper '!"…git-credential-manager.exe"'` —— 只挂 GCM
     4. 修复后有效凭据链**仅剩 GCM**，推送立即成功
   - **影响范围**：该修复写在**全局**配置中，对本机所有 git 仓库生效，可避免其他项目出现同样的卡死
   - **回滚方式**：`~/.gitconfig.bak-20260930` 即修复前副本
   - **结论**：本机推送链路现已完全正常，`git push` 秒级返回

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/development-plan.md` | **重大更新**。1.0 → 2.0，新增第八、九节（正式实施计划 + 版本记录续），状态由「待评估」变为「已选定」 |
| `.gitignore` | 更新。新增 `dist/` 排除项 |
| `documents/progress.md` | 更新。追加本小节 |
| `documents/architecture.md` | 更新。变更记录补 1.8；`.gitignore` 描述补 `dist/` |
| 代码 | **无改动。** 本版本仍为纯文档产出 |

### 当前可运行状态

- ✅ 本地与 GitHub 远程同步；1.5 / 1.6 / 1.7 三个提交已推送，远程 `main` 至 `735aefc`，本版本（1.8）的提交随后同步
- ✅ **git 推送链路故障已修复**（见本小节第 5 点），`git push` 由「永久卡死」恢复为秒级返回
- ✅ Git 凭据链路正常（GCM 承载，无需令牌）
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK 34 + build-tools 34.0.0）
- ✅ 页面结构与样式完成（静态，尚无交互）
- ✅ 需求文档、功能设计文档、开发计划文档（2.0 正式实施计划）就绪
- ⏳ **`review-app/www/app.js` 尚未编写 —— 应用仍为无交互的静态空壳**
- ⏳ Capacitor 工程尚未初始化；Android 原生工程尚未生成
- ⏳ APK 尚未产出（`dist/` 目录尚未创建）

### 待办清单

- [ ] **用户提供：手机品牌 + 安卓版本 —— 阻塞阶段一 `v0.0`**
- [ ] **用户提供：自定义复盘模板栏目（名称、顺序、是否带填写提示）—— 阻塞阶段三 `v0.2`**
- [ ] 用户对 8.12 中 7 条默认决定确认或提出异议
- [ ] 阶段一：Capacitor 工程初始化 → 安卓原生工程 → 语音识别原生插件 → 分段续录调度 → 编译 `v0.0` 语音验证包
- [ ] 阶段二：数据结构、模板层、记录页、保存流程、列表、详情 → `v0.1`
- [ ] 阶段三：设置页、DeepSeek 调用层、提示词、解析容错、异常降级 → `v0.2`
- [ ] 阶段四：文件写入插件、`.md` 生成器、Obsidian 镜像写入、导出分享 → `v0.3`
- [ ] 阶段五：检索、数据看板、周月汇总、设置页收尾 → `v1.0`
- [ ] 真机安装测试 → 各阶段验收 → 最终连续 7 天试用

---

## 1.9 — 2026-09-30 — 用户提供手机与模板；查出一处影响方案可行性的风险

### 本版本做了什么

1. **用户提供了两项开工前置输入**
   - 手机：**OPPO Reno14**（ColorOS 15 / Android 15，联发科天玑 8350，2025-05 发布）
   - 自定义模板栏目：**完成任务汇总 / 卡点疑惑 / 感受与收获 / 明日代办**（按用户原文记录）
   - 8.3 与 8.13 中的待办项全部解除

2. **查出一处高风险问题：国内 OEM ROM 上系统语音识别可能根本用不了**（本版本最重要的事）
   - **背景**：原设计的语音方案依赖安卓系统自带的 `SpeechRecognizer`（即 `RecognitionService`），它依赖 Google 语音服务
   - **事实**：国内 OEM ROM（**小米 / 华为 / OPPO / vivo**）普遍不预装 Google 语音服务，导致
     ```java
     SpeechRecognizer.isRecognitionAvailable(context)   // 返回 false
     ```
   - **关键澄清**：`false` **不等于没有引擎**。2026 年 3 月一份开源项目修复（openclaw#38691）指出这些机型**自带可用的 OEM 语音引擎**（例：小米 `com.xiaomi.mibrain.speech/.asr.AsrService`），只是没注册成默认服务。正确做法是绕开该检测，读 `Settings.Secure.voice_recognition_service` 拿到组件名后用 `createSpeechRecognizer(context, componentName)` 定向调用；同时 `targetSdk ≥ 31` 必须在 Manifest 里声明 `<queries>`
   - **OPPO 的不确定性**：有较早资料称 OPPO 自 7.0 起该方法即返回 `false` 且当时无解；但 ColorOS 自带「录音转文字」「AI 语音摘要」，说明本机**存在 ASR 引擎**，只是**是否以标准 `RecognitionService` 开放给第三方应用无法从公开资料确定**
   - **结论：必须真机实测，不能靠推断** —— 这正是方案二把语音验证放在最前的原因

3. **阶段一的验证方式据此升级：先用 adb，不先打 APK**
   - 原计划是写一个语音探针 APK 去试。后发现 Android SDK 的 `platform-tools` 已就位、`adb` 可直接用（版本 37.0.1），**查系统配置的成本近乎为零**
   - 新增「第 0 步」实测步骤（无需安装任何东西，约 1 分钟）：
     ```bash
     adb shell settings get secure voice_recognition_service
     adb shell pm list packages | grep -iE "speech|voice|asr|iflytek|oppo"
     ```
   - 判定：返回有效组件名 → 系统识别大概率可走通；返回 `null` 且无相关包 → 转云端 ASR
   - 只有 adb 判断不了「引擎能否真的出字」时，才打最小探针 APK 兜底

4. **备选方案重新核实：云端 ASR 已可免费，且收益比预期大**
   - 硅基流动（SiliconFlow）官方价格页标注 `FunAudioLLM/SenseVoiceSmall`、`Qwen/Qwen3-ASR-1.7B`、`XingChenASR-V3.2` **均为 0 元**
   - 前置条件：需注册账号并**完成实名认证**（官方公告：自 2026-05-15 起未实名无法使用平台功能）
   - **反向收益**：系统识别有 **60 秒单次上限**，正是「分段续录」这套最复杂机制的来源。**改走云端识别，分段续录整套复杂度直接消失**，且中文识别准确率更高。也就是说原方案里唯一"可能根本做不成"的风险点被移除，换来的方案反而更简单
   - 代价：音频需上传到服务商；需联网（可先离线录音、联网后补转写）
   - **处理方式**：先用 adb 实测，结果出来再决定是否启用，**不提前改动 5.2 的设计**

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/functional-design.md` | **更新至 1.1**。第十一节补记模板栏目定义（待定事项 1 解除）；新增**第十三节「语音识别可用性风险」**（问题、正确适配方式、adb 实测步骤、云端 ASR 备选对比）；第十二节末尾加指向说明 |
| `documents/development-plan.md` | **更新至 2.1**。8.3 补记三项前置已提供；8.4 阶段一新增「第 0 步 adb 探测」并重写"你需要做"；8.4 补入云端 ASR 备选对比；8.13 标记待办解除；新增 2.1 版本记录 |
| `documents/progress.md` | 更新。追加本小节 |
| `documents/architecture.md` | 更新。变更记录补 1.9；文件结构无变化 |
| 代码 | **无改动。** 本版本仍为纯文档产出 |

### 当前可运行状态

- ✅ 代码与文档均已同步至 GitHub（`main` = `65f4393`，本版本提交随后推送）
- ✅ git 推送链路正常，`git push` 秒级返回，无需令牌
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK 34 + build-tools 34.0.0 + **platform-tools / adb 37.0.1**）
- ✅ 需求文档、功能设计文档（1.1）、开发计划（2.1 正式实施计划）就绪
- ✅ 开工前置输入已齐（手机型号 + 模板栏目）
- ⏳ **阶段一第 0 步（adb 探测语音识别可用性）尚未执行 —— 等用户开启 USB 调试并连接手机**
- ⏳ `review-app/www/app.js` 尚未编写 —— 应用仍为无交互的静态空壳
- ⏳ Capacitor 工程尚未初始化；APK 尚未产出

### 待办清单

- [ ] **【当前阻塞】用户开启手机 USB 调试并插线** → 我执行 adb 探测，得出「系统识别可走通 / 不可用」的结论
- [ ] 依探测结论二选一：
  - 可走通 → 写原生插件（含 `Settings.Secure.voice_recognition_service` 适配 + Manifest `<queries>`）+ 分段续录调度
  - 不可用 → 与用户确认改走云端 ASR，并**同步修订 `functional-design.md` 5.2 / 5.3 与里程碑（分段续录可删）**
- [ ] 阶段一：Capacitor 初始化 → 安卓工程 → 语音插件 → 编译 `v0.0` 探针包
- [ ] 阶段二 ~ 阶段五：按 `development-plan.md` 第八节执行

---

## 1.10 — 2026-09-30 — 模板栏目定稿；云端 ASR 方案实现难度评估

### 本版本做了什么

**① 模板栏目修正（用户确认）**

- 第 4 项由「明日代办」修正为 **「明日待办」**
- 修正后的最终模板：**完成任务汇总 / 卡点疑惑 / 感受与收获 / 明日待办**
- 1.9 小节中按用户原文记录的「代办」保留不作修改，以维持历史快照的可追溯性

**② 云端 ASR 方案的实现难度评估（用户追问「用云端技术难度怎么样」）**

评估结论：**技术难度低于系统识别方案**，因为原本最难的一块会整体消失。

| 环节 | 系统识别（原方案） | 云端 ASR |
| --- | --- | --- |
| 录音 | 需写原生 Java 插件并桥接 | WebView 自带 `MediaRecorder`（WebView Android 47+ 完整支持），纯 JS |
| 转文字 | 原生插件回调 + 绕 `isRecognitionAvailable()` 陷阱 | 一次 HTTP POST，与 DeepSeek 调用同构 |
| 分段续录 | **必须自建**（全项目最复杂机制） | **整块删除** |
| 原生代码量 | 约 300~500 行 Java | **0 行** |
| 新增 Web 代码量 | — | 约 80~120 行 |

**已核实的免费事实**：硅基流动官方价格页将 `FunAudioLLM/SenseVoiceSmall` 标注为 **¥0**（同页 `Qwen/Qwen3-ASR-1.7B`、`TeleAI/TeleSpeechASR` 亦免费）。接口为 `POST https://api.siliconflow.cn/v1/audio/transcriptions`，兼容 OpenAI 格式。

**前置门槛**：自 **2026-05-15** 起，未完成实名认证的账户无法使用平台功能——实名是使用免费模型的前提。

**两个待实测的未知数**（详见 `functional-design.md` 13.6）：

1. **录音格式兼容性** —— WebView 通常录出 `audio/webm;codecs=opus`，而服务商常规支持 mp3/wav/ogg/m4a/flac，webm 不在列。三条出路：直接传 webm 试 → 让 WebView 录 mp4/aac → 退回原生 `MediaRecorder` 录 ogg。
2. **单文件时长/大小上限** —— 官方文档未获取到明确数字，第三方说法矛盾。但容量估算（opus 约 64kbps，5 分钟约 2.4MB）显示即使按最保守的 10MB 限制，10 分钟内口述也不触顶。

**验证成本**：两个未知数**都不需要写完整应用**。其中「接口连通性 + webm 是否被接受」**可全程在电脑上完成**，不依赖手机，因此它成为新的关键路径。

**③ 路线选择（待用户拍板）**

向用户提出三个待决问题：是否接受实名认证、是否接受音频上传至服务商、是否将主路线切换为云端 ASR。**未擅自改动 5.2 分段续录等设计**，等用户确认后再同步修订。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/functional-design.md` | 1.1 → **1.2**。第十一节模板栏目第 4 项修正为「明日待办」；第十三节新增 13.5~13.8（难度评估、两个未知数、验证成本、路线选择）；第十四节版本记录补 1.2 |
| `documents/development-plan.md` | 模板栏目同步修正为「明日待办」（8.13 前置项） |
| `documents/progress.md` | 本小节（1.10） |
| `documents/architecture.md` | 变更记录补 1.10 |

### 当前可运行状态

- ✅ 模板栏目**已定稿**，不再有未决输入
- ✅ 云端 ASR 方案的技术难度、费用、前置条件、风险点均已查清并成文
- ⏳ **应用代码仍未动** —— `review-app/www/app.js` 未编写，Capacitor 工程未初始化，APK 未产出
- ⏳ 阶段一尚未开工

### 待办清单

- [ ] **【当前阻塞】用户就云端路线回答三问**：是否接受实名认证 / 是否接受音频上传 / 是否切换主路线
- [ ] 若切换 → 用户注册硅基流动并取 Key → 我在电脑上直接打接口验证（接口连通性 + webm 是否被接受 + 返回结构）
- [ ] 若切换 → 同步修订 `functional-design.md` 5.2（分段续录可删）、5.3、第十节里程碑，以及 `development-plan.md` 的 `v0.0` 探针定义
- [ ] 若不切换 → 仍走 1.9 的 adb 探测路径（需用户开启 USB 调试并插线）
- [ ] 阶段一：Capacitor 初始化 → 安卓工程 → 语音链路 → 编译 `v0.0` 探针包
- [ ] 阶段二 ~ 阶段五：按 `development-plan.md` 第八节执行

---

## 1.11 — 2026-09-30 — 项目由 C 盘迁移至 D 盘

### 本版本做了什么

**① 迁移动因**

用户要求把项目从系统盘移出。目标位置定为 `D:\Projects\review-record\`（与 GitHub 仓库同名）。

**② 迁移前的依赖排查**

跨盘迁移的风险不在文件本身，而在**写死的绝对路径**。逐项排查结果：

| 检查项 | 结果 |
| --- | --- |
| `review-app/`（应用代码） | 无路径依赖 |
| `documents/`（文档） | 无路径依赖 |
| `.workbuddy/memory/`（项目记忆） | 1 处路径描述（`MEMORY.md` 第 59 行），**已修正** |
| `.git/config` | **1 处硬编码**：`http.sslCAInfo`，**已修正** |
| `.git/hooks/` | 无自定义脚本 |
| `.android-build/setup.sh` | **1 处硬编码**：`TOOLS=` 变量，**已修正** |
| Android SDK / JDK 内部 | **无绝对路径**，工具链本身可移植 |
| WorkBuddy 会话索引 | `~/.workbuddy/changes-index/` 留有旧路径，仅影响历史变更记录，无实际功能影响 |

**③ 执行方式**

- 采用**复制而非剪切**：跨盘剪切本质是"复制 + 删除"，中断会导致数据半途丢失
- 复制工具：优先 `robocopy /E /COPY:DAT /R:1 /W:1`，中断后以 `cp -r` 补齐
- **跳过 329M 无用文件**：`jdk17.zip`（182M）、`cmdline-tools.zip`（147M）为已解压安装包，`cmdtools/` 为空目录，三者均未复制
- 迁移前先 `adb kill-server`，避免 SDK 内文件被占用

**④ 迁移后已修正的三处路径**

| 文件 | 原值 | 新值 |
| --- | --- | --- |
| `.git/config` | `sslCAInfo = C:/Users/20472/WorkBuddy/2026-09-30-13-07-11/.git/win-root-ca.pem` | `sslCAInfo = D:/Projects/review-record/.git/win-root-ca.pem` |
| `.android-build/setup.sh` | `TOOLS="/c/Users/20472/WorkBuddy/2026-09-30-13-07-11/.android-build"` | `TOOLS="/d/Projects/review-record/.android-build"` |
| `.workbuddy/memory/MEMORY.md` | 工作区根目录记为 C 盘路径 | 更新为 D 盘路径，并补记「打开项目需手动选工作空间」 |

**⑤ C 盘源目录处理**

**C 盘源目录保持原样未删除、未修改**，作为迁移后的回退点。待用户在 D 盘副本上确认无误后，由用户自行删除。

**⑥ 迁移收尾时的全量扫描，追加修正两处**

迁移完成后对 D 盘副本做了一次全量旧路径扫描（含隐藏目录）。结果中除历史快照（`progress.md` 早期小节、`MEMORY.md` 迁移备注）按「只增不删」规则保留外，另发现两处**面向未来**的路径描述，已修正：

| 文件 | 位置 | 说明 |
| --- | --- | --- |
| `documents/architecture.md` | 第一节目录结构总览 | 树根标签由旧目录名改为 `review-record\` 并注明完整路径 |
| `documents/development-plan.md` | 交付机制表「产物位置」 | APK 目标目录由旧路径改为 `D:\Projects\review-record\dist\` |

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `.git/config` | `sslCAInfo` 指向新路径 |
| `.android-build/setup.sh` | `TOOLS` 指向新路径 |
| `.workbuddy/memory/MEMORY.md` | 工作区根目录路径更新 |
| `documents/progress.md` | 本小节（1.11） |
| `documents/architecture.md` | 变更记录补 1.11 |

### 当前可运行状态

- ✅ 项目已在 `D:\Projects\review-record\` 生成完整可用副本
- ✅ 代码、文档、git 仓库、项目记忆、编译工具链均已随迁
- ✅ 三处硬编码路径已修正
- ⏳ C 盘源目录待用户确认后自行删除
- ⏳ **应用代码仍未动** —— `app.js` 未编写，Capacitor 工程未初始化，APK 未产出

### 待办清单

- [ ] 用户在 D 盘目录验证：`git status`、`git log`、`git fetch origin` 均正常
- [ ] 用户在 WorkBuddy 新建任务时，**手动选择 `D:\Projects\review-record\` 作为工作空间**
- [ ] 确认无误后由用户删除 C 盘旧目录 `C:\Users\20472\WorkBuddy\2026-09-30-13-07-11\`
- [ ] **【仍为当前阻塞】用户就云端 ASR 路线回答三问**：是否接受实名认证 / 是否接受音频上传 / 是否切换主路线
- [ ] 阶段一：Capacitor 初始化 → 安卓工程 → 语音链路 → 编译 `v0.0` 探针包
- [ ] 阶段二 ~ 阶段五：按 `development-plan.md` 第八节执行

---

## 1.12 — 2026-09-30 — 语音链路切换为云端 ASR，三份文档同步修订

### 本次变化

**本版本为纯文档产出，未改动任何代码。**

**① 用户决策：语音路线三问全部答复「都可以」**

| # | 待决问题（`functional-design.md` 13.8） | 答复 |
| --- | --- | --- |
| 1 | 是否接受硅基流动实名认证 | ✅ 接受 |
| 2 | 是否接受音频上传至服务商 | ✅ 接受 |
| 3 | 是否切换主路线为云端 ASR | ✅ 切换 |

1.10 小节遗留的唯一阻塞项**就此解除**，且这是全项目**唯一会触发换方案**的决策点，至此已定。

**② 三份文档同步修订**

| 文档 | 版本 | 变化 |
| --- | --- | --- |
| `functional-design.md` | 1.2 → **1.3** | **新增第十五节「语音链路修订：改用云端 ASR」，为当前有效设计**；第一节 1.1 / 1.2、第五节 5.2、第十节里程碑就近打上废弃或修订标记（**原文一律保留未删**）；13.8 补记决策结果 |
| `development-plan.md` | 2.1 → **2.2** | 8.1 总览中 `v0.0` 重定义为**语音链路探针**、`v0.1` 内容改为「录音 + 上传转写」；**8.4 阶段一整节重写**（不再写原生语音代码，第一步改为在电脑上打接口）；8.5 阶段二步骤表重写并补验收项；8.12 第 2 条（55 秒阈值）作废并新增三条默认值；8.13 标注新的阻塞项 |
| `product-requirements.md` | 1.0 → **1.1** | 新增「补记（1.1）」一节：修正关键决策表的语音方案、**原风险 2 / 3 彻底消灭**、**新增三条风险**（隐私 / 联网 / 服务商依赖）、非功能性需求口径修正（文本仅存本机、音频转写后即删） |

**③ 这次切换到底改掉了什么（一句话版）**

> **产品要做的功能一件没少，砍掉的是实现难度最大、且可行性最不确定的那一块。**

| 项 | 旧（系统识别） | 新（云端 ASR） |
| --- | --- | --- |
| 可行性 | **不确定**——OPPO ColorOS 是否开放标准 `RecognitionService` 无法从公开资料确定 | **确定可行** |
| 分段续录 | **必须自建**（全项目最复杂机制） | **整块取消** |
| 原生代码量 | 约 300~500 行 Java | **0 行**（语音部分）；文件写入插件仍需 |
| 单次时长 | 约 60 秒 | 无硬上限 |
| 费用 | 免费 | 免费 |
| 代价 | 准确率约 82~85%；可能根本不可用 | 音频需上传；转写需联网（可先录后传） |

**④ 关键路径变了（重要）**

- **原**：先开 USB 调试 → `adb` 探测系统识别服务 → 再决定路线
- **新**：先在**电脑上**用一段测试音频直接打硅基流动转写接口（**不需要手机、不需要写应用、约 10 分钟**）
- `adb` 探测（`functional-design.md` 13.3）**降为可选动作**，不再阻塞任何阶段

**⑤ 顺带修正的一处环境记载**

上一轮对话完成了智能体身份文件的初始化（`~/.workbuddy/` 下的 `SOUL.md` / `IDENTITY.md` / `USER.md`，并删除 `BOOTSTRAP.md`）。此项不属于本仓库内容，仅在此留一笔。

### 涉及文件

| 文件 | 变化 |
| --- | --- |
| `documents/functional-design.md` | **重大更新**。1.2 → 1.3，新增第十五节（15.1~15.7），前文多处加废弃/修订标记，第十四节补 1.3 版本行 |
| `documents/development-plan.md` | **重大更新**。2.1 → 2.2，第八节多小节重写，第九节补 2.2 版本行 |
| `documents/product-requirements.md` | **更新**。1.0 → 1.1，新增「补记（1.1）」一节，第九节补 1.1 版本行 |
| `documents/architecture.md` | 更新。变更记录补 1.12；第四节 Capacitor 选型理由收窄、第五节数据流加变更标记、第六节 `app.js` 职责改写、文件清单第 9/10/11 条版本号更新 |
| `documents/progress.md` | 本小节（1.12） |
| 代码 | **无改动**。仍为纯文档产出 |

### 当前可运行状态

- ✅ **语音方案已定，且不再存在"可能根本做不成"的技术风险**
- ✅ 三份产品/设计/计划文档均已同步到新路线，**设计定稿**
- ✅ 代码与文档此前均已同步至 GitHub（本版本提交随后推送）
- ✅ 编译工具链完全就绪（JDK 17 + Android SDK 34 + build-tools 34.0.0 + adb 37.0.1）
- ⏳ **`review-app/www/app.js` 尚未编写** —— 应用仍为无交互的静态空壳
- ⏳ Capacitor 工程未初始化；APK 未产出；`dist/` 未创建

### 待办清单

- [ ] **【当前阻塞】用户注册硅基流动账号 → 完成实名认证 → 把 API Key 给我**，我在电脑上直接打接口验证（连通性 / `webm` 是否被接受 / 返回结构）
- [ ] 用户确认 `functional-design.md` 15.4 与 `development-plan.md` 8.12 新增的三条默认值（音频用完即删 / 软上限 10 分钟 / 格式优先级实测）
- [ ] 阶段一：Capacitor 初始化 → 安卓工程 → 极简录音上传页 → 编译 `v0.0` 语音链路探针
- [ ] 阶段二 ~ 阶段五：按 `development-plan.md` 第八节（2.2 版）执行

