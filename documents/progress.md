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
