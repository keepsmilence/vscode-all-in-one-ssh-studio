# All in One SSH Studio

[English](README.md) | [简体中文](README_zh.md)

VS Code 内的一站式 SSH 客户端：连接管理、SFTP 浏览、远程编辑、系统/网络/进程监控、端口转发，以及与 GitHub Copilot Chat 和外部 MCP 客户端集成。

> 不向远端推送任何 binary、不上报任何遥测、不在 `~/.ssh` 写文件。所有破坏性操作均强制二次确认。

侧边栏、对话框与全部功能面板会跟随 VS Code 显示语言，运行时界面完整支持 English 与简体中文。

---

## 30 秒上手

1. 左侧活动栏点击 **All in One SSH Studio** 图标 → 「连接」面板 → 标题栏 **＋** 新建连接。
2. 填写主机 / 端口 / 用户名 / 认证（密码 · 私钥 · SSH Agent），点 **「测试连接」** 验证（不会写任何配置）。
3. 通过后保存并连接；连接成功后右侧的「仪表盘」与「工具集」自动可用。

跳板机场景：先把已在白名单内的主机配为普通连接，然后在目标连接「高级 → 跳板机（ProxyJump）」下拉里选中它。支持多级嵌套（A→B→C）。

---

## 视图与入口

打开活动栏 **All in One SSH Studio** 后能看到 5 个面板：

| 面板 | 用途 |
|---|---|
| **连接** | 支持多级分组，新建 / 编辑 / 连接 / 断开；连接配置数据支持导入导出；右键菜单的「断开」带二次确认 |
| **活动会话** | 当前活动会话，支持断开连接（带二次确认） / 新建SSH终端 / 打开SFTP游览器 |
| **仪表盘** | 单面板内分块展示「CPU / 内存 / 网络 / 磁盘 / 系统信息」；CPU 与内存独立分块带 5 分钟 mini sparkline 走势；进度条按阈值（warn ≥75% / crit ≥90%）变色 |
| **工具集** | 卡片式聚合入口，按下面两大类组织所有功能面板（活动会话相关） |
| **插件管理** | 全局工具入口，与会话生命周期解耦：插件使用说明 / 待办任务管理 / 操作审计日志 |

### 工具集 · 资源管理

| 卡片 | 说明 |
|---|---|
| **SFTP 游览器** | 浏览 / 上传 / 下载 / 改名 / 删除；支持从 VS Code 资源管理器多选并拖入当前远端目录；多选（Ctrl/Cmd 切换、Shift 范围）+ 右键批量删除 / 批量下载；删除二次确认 + 父目录写权限检查；批量同名一次性决策（全覆盖 / 全跳过 / 全保留两者 / 逐个询问）；**右键菜单**（进入目录 / 在终端中打开 / 复制路径）；`rss.sftp.writeConfirm` 可开启在线文件保存前二次确认 |
| **命令片段** | 全局 / 按连接绑定的命令模板；卡片网格视图 + 弹窗详情 / 编辑；展示全局与当前连接的片段；操作：查看详情 / 编辑 / 复制 / 填入终端 / 填入并执行 / 重置使用计数 / 删除；危险命令受白名单拦截 |
| **端口转发** | local→remote / remote→local 两种模式；表格展示状态 / 当前并发 / 上下行字节累计；端口冲突自动探测；查源端口对应远端进程 |
| **日志查看** | 收藏远端日志路径（支持模板变量如日期）；**实时模式**（tail -F，ring-buffer，可暂停 / 继续 / 清空）与 **分页模式**（字节偏移翻页，大文件无需全文扫描）；关键词搜索（grep -bnF）+ n/N 跳转；前端语法着色（时间戳 / 日志级别 / JSON Key / IP / URL）；多标签页 |
| **传输队列** | 文件上下载进度查看、暂停、重试；面板已打开时新上传任务在后台刷新，不抢占 SFTP 标签页；连接断开时该连接的排队任务立即取消，运行中任务在下一检查点自动终止 |
| **待办任务** | 全局运维事项备忘清单；待办内容：标题 / 备注 / 优先级 / 截止时间 / 标签 / 关联连接；支持状态过滤、批量清理 |

### 工具集 · 系统管理

| 卡片 | 说明 |
|---|---|
| **进程管理** | 列表 + 排序指示 + 过滤 + 4 Tab 详情（概要 / 环境变量 / 文件描述符 / 内核堆栈）；详情面板可拖动调高；支持 KILL 进程 |
| **端口监控** | 监听端口 + ESTABLISHED 双面板，可拖拽调整布局；列可排序、双击打开 GeoIP 详情 |
| **命令历史** | 汇总当前用户的 shell / REPL 历史（bash / zsh / fish / sh / python / node / mysql / psql / sqlite / redis），按时间倒序；每行可一键复制或发送到当前终端 |
| **服务管理** | systemd 服务列表（服务名 / 状态 / 开机启动）；行点击下方 tail journal；启动 / 停止 / 重启 / 启用 / 禁用含确认 |
| **计划任务** | 三 Tab：cron（含 `/etc/crontab` + `cron.d` + 各用户 crontab）、systemd timers、at queue；行点击展示触发的 service / unit 文件 / cron 字段解析 |
| **安全审计** | 五 Tab：当前在线（who/w）、登录历史（last）、失败尝试（lastb）、sshd 失败日志（auth.log/journalctl）、用户与用户组 |
| **软件与环境** | 四 Tab：已安装包（带搜索）、仓库源（apt/yum/apk/pacman）、环境变量、PATH 命令（按需扫描） |
| **容器管理** | docker / podman 一致 API；列出全量容器（含已停止）+ 端口映射；详情展示 state/ports/mounts/networks/env；一键复制完整容器 ID、把挂载主机目录直接在 SFTP 中打开；Start/Stop/Restart 与 logs tail 200；兼容 podman 模拟 docker CLI 的提示输出 |
| **防火墙管理** | **概览**：检测并展示 iptables / ip6tables / nftables / ufw / firewalld / Fail2Ban 安装与运行状态；service 开启停止控制。**iptables / ip6tables**：table（filter/nat/mangle/raw）与 chain 切换；规则表格；高风险（全段 ACCEPT）红色 / 中风险橙色高亮。**nftables**：ruleset 原始输出带风险高亮。**ufw**：编号规则表 + 添加 / 删除规则 + 开启关闭。**firewalld**：zone 列表 + 服务控制 + permanent 重载。**Fail2Ban**：jail 状态 + 封禁 IP 列表 + 一键解禁 |
| **资源告警** | 后台轮询 CPU / 内存 / 磁盘 / 负载 / Swap，持续超阈时右下角通知（默认关闭，开启见 `rss.alerts.enabled`）；三层抑制：连续触发次数 · 冷却时间 · 当日不再提醒；通知附带「查看仪表盘」快捷按钮 |
| **依赖检查** | 一键体检远端 ss/netstat/procps/iproute2 等 9 类必备工具，按发行版给出安装命令 |

### 工具集 · 插件管理

> 已迁移：插件管理已从「工具集」独立为侧边栏顶级面板，详见下方「插件管理」面板。

### 插件管理（独立侧边栏面板）

| 卡片 | 说明 |
|---|---|
| **插件使用说明** | 查看插件的使用说明和功能介绍（即欢迎页） |
| **操作审计日志** | JSONL 持久化每次终端 / 面板 / Copilot 工具调用；4+1 标签页（全部 / 终端 / 面板 / Copilot / 错误）；支持按日期 / 连接 / 关键词过滤；支持实时模式、导出、清空；保留天数可配置 |

---

## SSH 终端

集成 xterm；启用「终端目录联动」后，终端中 `cd` 会通过 OSC 7 同步 SFTP 资源管理器目录（默认关闭，可在 SFTP 面板或设置中开启）。

## 自动账号切换（连接级）

在连接编辑器中开启 sudo / su 切换：交互终端启动后注入 `sudo -i -u` 或 `su - user`；exec 通道使用 `printf base64 | base64 -d | sudo -n -H -u 'user' bash -s` 包装，无 shell 注入风险；所有 Copilot 工具调用同样走升权通道。

## 远端编辑

双击远端文件下载到本地缓存，保存自动回写。缓存路径可在 `rss.editor.tempDir` 设置；留空则使用扩展存储下的 `cache/`。

---

## Copilot Chat 工具

本插件默认通过 VS Code 原生 Language Model Tools 把 SSH/SFTP/系统能力集成到 GitHub Copilot Chat。原生通道仍然**无需启动服务，也无须配置 `.vscode/mcp.json`**，工具发现、授权与生命周期均由 VS Code 管理。`rss.copilot.toolChannel` 可切换为 VS Code MCP provider；默认值 `native` 会避免同一能力在 Copilot 工具列表中重复出现。面向其他编辑器的可选 MCP 兼容通道见下文。

### 可用工具

| 工具（`#` 引用名） | 说明 |
|---|---|
| `#sshListConnections` | 列出 SSH 连接、嵌套分组路径、主/备用地址及用户录入的内外网标签；返回结构化到期状态，临近到期时可主动提醒（不含敏感字段） |
| `#sshExec` | 在指定连接执行命令；命中危险命令黑名单直接拒绝；默认弹窗二次确认 |
| `#interactiveStart` / `#interactiveRead` | 以受控 PTY 启动具体交互式管理工具，并按 cursor 增量读取菜单 / 问答输出 |
| `#interactiveSend` / `#interactiveClose` | 每次确认后发送一行非敏感输入，或终止交互会话；密码 / 口令 / OTP 强制转真实终端 |
| `#sftpList` / `#sftpRead` / `#sftpWrite` | 远程目录列表、文本读写（`>1MB` 不许读，请改用 `#sftpDownload`） |
| `#sftpUpload` / `#sftpDownload` | 单文件上传 / 下载；上传到受保护路径会被拒绝 |
| `#sftpUploadDir` | 递归上传本地目录（需二次确认）；冲突策略 `overwrite`（默认）或 `skip` |
| `#systemInfo` | 抓取 `uname -a; uptime; free -h; df -h` |
| `#topProcesses` / `#findProcesses` / `#killProcess` | 按 CPU/内存查 top；ps args 子串匹配；KILL 含二次确认与信号 / PID 白名单 |
| `#inspectPort` | 端口监听情况 + ESTABLISHED 连接数 + 去重远端 IP 数 + 可选样本 |
| `#tailLog` | `tail -n` 一次性快照（非流式），可选 `grep -F` 过滤 |
| `#listSnippets` / `#runSnippet` | 列出当前可用片段；填入终端可选自动回车（受危险命令白名单与连接绑定校验） |
| `#listPortForwards` | 列出端口转发配置 + 实时状态（连接数 / 字节累计 / 最后错误） |
| `#listServices` | 列出 systemd/sysv 服务及其 active/enabled 状态；支持按名称过滤、只看运行中 |
| `#serviceLogs` | 读取指定服务（unit）的 `journalctl` 日志快照，可选子串过滤 |
| `#listScheduledTasks` | 汇总 crontab / systemd timer / at 队列；可选附带某个 timer 的详情 |
| `#firewallOverview` | 检测 iptables/ip6tables/nftables/ufw/firewalld/fail2ban 的安装、运行、开机自启状态 |
| `#listPackages` | 已安装软件包（可按名称过滤）+ 仓库源；`includeEnv` 时附带环境变量（敏感字段自动脱敏为 `***`） |
| `#processDetail` | 单个 PID 的命令行 / 工作目录 / 打开文件等详情；`includeEnv` 时附带进程环境变量（同样自动脱敏） |
| `#securityAudit` | 当前在线用户、最近成功/失败登录、sshd 认证失败摘要，及可选的用户/组/sudoers 概览 |

> 待办任务管理仍保留在插件「工具集」中，但不再注册为 Copilot Chat 工具，避免占用工具列表和模型选择上下文。

### 使用方式

1. 在《连接》面板中先连上目标主机；
2. 打开 Copilot Chat，输入 `#`，在弹出的工具列表中选择 `sshExec` / `sftpList` 等；或直接描述需求让 Copilot 自行选工具；
3. 破坏性操作会被 VS Code 原生二次确认 UI 拦下来。

### 外部 MCP 客户端

Cursor、TRAE 及其他 MCP 客户端可以使用与 GitHub Copilot Chat 相同的完整 SSH 工具集。

1. 在插件设置中启用 `rss.mcp.enabled`。
2. 在同一设置项说明中点击「复制当前工作区的 MCP 客户端配置」。如果当前编辑器未显示该链接，可从命令面板运行「All in One SSH Studio: 复制 MCP 客户端配置」。
3. 把复制出的 `mcpServers` 对象粘贴到目标客户端的 MCP 配置中；使用工具期间请保持当前插件窗口打开。
4. 如需让 VS Code Copilot 使用 MCP 工具，把 `rss.copilot.toolChannel` 设为 `mcp`。建议保留默认的 `native` 以避免工具重复；`both` 可能让同一工具出现两次。

默认的 `rss.mcp.confirmationPolicy=riskBased` 会把只读、非破坏性工具的审批交给 Copilot、Cursor 或 TRAE 自身，不额外显示扩展模态框；命令执行、写入、上传下载、KILL、交互输入和终端片段等状态变更或破坏性工具仍由扩展强制确认，并复用原生工具的命令、路径、PID 等操作详情。设为 `alwaysPrompt` 可恢复每次调用都二次确认。所有 MCP 调用都会按工具、客户端声明和目标连接写入审计，参数复用原生工具的凭证脱敏与命令/正文截断规则；SecretStorage 中的密码、私钥和 passphrase 不会进入 MCP 结果。远端输出、文件/日志内容、进程详情等获准结果会按所选客户端及模型提供方的隐私策略处理。

> **macOS 上的 TRAE：**请始终使用插件生成的配置。手工填写 TRAE Helper 路径时，路径中的空格可能导致启动失败。

扩展重载后，如果只有一个 All in One SSH Studio 窗口在提供 MCP 工具，已有配置会自动重新连接；若多个工作区窗口同时启用 MCP，请从目标窗口重新复制配置。

### 部署场景示例

```
帮我用 prod-1 连接部署一下 seller-api：
1. 在 /data/api/seller-api 执行 ./service.sh backup；
2. 把本地 /Users/me/work/seller-api/target/seller-api.jar 传到远程同名位置；
3. 把本地 target/lib/ 递归传上去覆盖远程 lib/；
4. 执行 ./service.sh restart；
5. 读一下 log/seller-api.log 最后 200 行，看看启动有没有报错。
```

Copilot 会拆成：`#sshExec backup` → `#sftpUpload jar` → `#sftpUploadDir lib` → `#sshExec restart` → `#tailLog log`，每一步危险操作都会跳出 VS Code 原生确认框。

---

## 数据安全与隐私

### 你的数据存在哪里

| 数据类型 | 存储位置 |
|---|---|
| 主机 / 端口 / 用户名 / 分组 / ProxyJump 引用 | `connections.json`（VS Code globalStorage）—— JSON 明文，仅本机可读，**不写入 `~/.ssh`** |
| 密码 / 私钥内容 / passphrase / 提权密码 / GeoIP API Key | **VS Code SecretStorage**（macOS Keychain · Windows Credential Vault · Linux libsecret） |
| 主机指纹 | `known_hosts.json`（同 globalStorage）；首次 TOFU 写入，更换会显式弹窗 |
| 远程文件本地缓存 | `cache/` 子目录或 `rss.editor.tempDir`，仅本地 |
| 操作审计日志 | `globalStorage` 下的 `audit/`，本地 JSONL、保留期可配置；不持久化命令正文、LM 文件内容和交互输入 |
| MCP 本地服务数据 | `globalStorage` 下的 `mcp/`；只含本地连接信息和轮换授权 token，不含 SSH 凭证 |

### 网络出站

- 插件本身**不上报任何遥测**，不向作者服务器发送任何数据。
- SSH / SFTP 流量**仅**发往你自己配置的主机（或经由你自己配置的跳板机）。
- **GeoIP 解析**默认关闭；开启后仅把 ESTABLISHED 列表的对端公网 IP 发给你选定的 provider，API Key 保存在 SecretStorage。
- **外部 MCP 接入**默认关闭；VS Code Copilot 默认使用原生工具通道。启用 MCP 后，插件与 MCP 客户端之间的通信只在本机进行；获准的工具结果最终发往哪里由所选客户端及其模型提供方决定。
- 调用 Copilot Chat 工具时，结果可能包含连接元数据、远端命令输出、文件或日志内容、进程详情及登录记录。VS Code 会依据你选择的模型提供方及其隐私条款，把工具结果交给该模型；插件本身不经过作者服务器，也不运行额外中转服务。

### 配置导入 / 导出

「连接」面板标题栏的 ☁⏫ / ☁⏬ 即为导出 / 导入；命令面板搜「导出连接配置」/「导入连接配置」也可。

| 模式 | 说明 |
|---|---|
| **加密导出（推荐）** | PBKDF2-SHA256（200k 迭代）派生密钥 + AES-256-GCM；导入需同一口令；文件被篡改会解密失败 |
| **仅结构** | 导出连接元数据、分组层级、命令片段、端口转发定义和日志收藏，**不含凭证**；共享前仍应检查主机名、用户名、备注、路径与命令 |
| **明文导出** | 必须经 modal 二次确认；仅供本地临时使用，使用后应立即删除 |

导入时所有 ID 重新分配，**绝不会覆盖现有连接**；ProxyJump 引用按映射重写。

### SFTP / Copilot 操作的统一保护层

写 / 删 / 重命名 / 上传 / 创建文件夹 都走统一的路径保护层，**无论触发源是 SFTP 浏览器、VS Code 内置文件资源管理器（`rss-sftp://`）、终端 OSC `rss-edit` 还是 Copilot Chat 的 `#sftp*` 工具**，规则一致：

- 受保护根路径（精确匹配，仅拦截【目录本身】被当成目标）：`/ /bin /sbin /boot /dev /lib /lib64 /proc /sys /usr /var /root /home /opt /run /srv /mnt /media`
- 受保护子树前缀（命中后其下任意子文件都拒绝）：`/bin/ /sbin/ /boot/ /dev/ /lib/ /lib64/ /proc/ /sys/ /usr/ /root/`
  - 默认 **不再包含 `/etc/`** —— 编辑 `/etc/nginx/*.conf`、`/etc/systemd/**` 等服务配置是合法运维场景；如果你希望严格保护，可在配置中把 `"/etc/"` 加进 `rss.security.protectedSubtrees`
- 敏感文件名（即便父目录未命中子树，basename 命中后仍拒绝）：`.ssh/{authorized_keys,known_hosts,config,id_*}`、`sudoers(.d/*)`、`shadow`、`gshadow`、`passwd`、`crontab`

**路径规范化** 严格处理特殊字符：拒绝控制字符（NUL / CR / LF / TAB / DEL）；折叠 `//`；解析 `.` / `..` 并阻断越根越界；POSIX 语义下 `\` 视作普通字符不当作分隔符。非法正则会被静默忽略并写入输出日志，不会让插件崩溃。

**人工旁路**：交互式来源（SFTP 浏览器 / 文件资源管理器 / OSC `rss-edit`）命中拦截时会弹出模态对话框，提供「已知晓，继续执行」按钮，点击后**仅本次操作**被放行，并写一条 warn 级别审计日志（含路径、操作类型、连接名）。如不希望任何旁路，可把 `rss.security.allowBypass` 设为 `false`，所有拦截都是硬阻断。**Copilot Chat 工具调用永远是硬拦截，不受 `allowBypass` 影响**——避免 AI 自助绕过。

可配置项：

| 配置项 | 说明 |
|---|---|
| `rss.security.protectedPaths` | 精确匹配的受保护根路径列表 |
| `rss.security.protectedSubtrees` | 子树前缀列表（条目以 `/` 开头与结尾，缺尾部 `/` 会自动补齐） |
| `rss.security.sensitiveFilePatterns` | 敏感文件名正则数组（i 模式） |
| `rss.security.interactiveCommandMode` | 受控交互策略：`strict`（默认，真实路径白名单）/ `auto`（自动允许可信系统程序） |
| `rss.security.interactiveAllowedCommands` | `strict` 模式可选的可信远端可执行文件真实绝对路径；`auto` 模式忽略 |
| `rss.security.allowBypass` | 交互来源是否允许「已知晓，继续执行」一次性放行（默认 `true`） |

`#sshExec` 命中危险命令（`rm -rf /`、`mkfs`、`dd of=/dev/`、`shutdown`、防火墙清空、`crontab -r`、`curl|sh`、内核参数写入 等）会被直接拒绝，错误信息会引导用户「请手工在真实终端中以适当身份（sudo）执行」。**命令级危险白名单不提供任何「绕过开关」**。

### 受控交互会话

`#interactiveStart` 会先通过 `command -v` + `realpath` / `readlink -f` 解析真实可执行路径。`rss.security.interactiveCommandMode` 默认 `strict`，要求命中可选路径白名单；切换为 `auto` 后，root 拥有、组/其他用户不可写且不位于用户/临时目录中的常规程序可自动放行。两种模式都会拒绝系统级危险命令、shell 语法、凭证参数，以及 shell / 解释器 / 命令包装器 / 可逃逸程序。`#interactiveRead` 使用 cursor 增量读取，并把远端输出视为不可信数据；`#interactiveSend` 每次发送前展示完整单行输入并强制确认，同时拒绝 shell 元字符并复用危险命令黑名单；`#interactiveClose` 负责终止并释放 PTY。

- 每个连接最多 2 个活动会话，全局最多 10 个；运行中 15 分钟无活动自动终止，结束后的输出保留 5 分钟。
- 输出缓冲上限 256 KiB，单次最多返回 48 KiB；ANSI / OSC 控制序列会在交给模型前移除。
- 常见密码、token、API key 等输出值会先脱敏；检测到密码、passphrase、OTP、PIN、token、验证码等输入提示时立即封锁并终止会话，模型不得继续发送；必须改用真实 SSH 终端。
- `#interactiveSend` 的输入正文不会写入审计日志，只记录长度、连接、会话和是否回车，避免误传秘密后二次落盘。

### 凭证最佳实践

- 优先用 **SSH Agent** 或 **带 passphrase 的私钥**，避免裸密码；
- 关键服务器配置 **跳板机**，缩小公网暴露面；
- 使用 **「测试连接」** 而非直接连接，可在不写 `known_hosts` 的前提下验证认证。

---

## 远端会执行哪些指令？

本插件不安装任何 agent、不下载任何二进制到远端；所有功能都基于 OpenSSH 自带能力 + 通用 Linux 工具。下列命令均按用户操作触发，**无后台静默调用**。

| 面板 / 操作 | 权限 | 典型命令 |
|---|---|---|
| 建立 SSH / SFTP | 普通用户 | OpenSSH 标准握手；SFTP 子系统 `ls/stat/open/read/write/mkdir/rename/remove` |
| Copilot 受控交互 | 取决于已确认命令 | 使用 SSH PTY 执行用户确认的具体管理工具；逐轮非敏感输入均单独确认，不开放通用 shell，不安装远端 agent |
| 终端 cwd 同步（可关闭） | 普通用户 | `PROMPT_COMMAND` / `precmd` 注入 OSC 7 |
| 仪表盘 · 系统 / 网络 | 普通用户读 | `uname / uptime`、`/proc/{loadavg,meminfo,cpuinfo,net/dev}`、`df -P`、`ip -s link` 兜底 `ifconfig` |
| 进程管理 | 普通用户读 · KILL 需相应权限 | `ps -eo …`、`/proc/<pid>/{status,environ,fd,stack}`；KILL 显式二次确认 |
| 端口监控 | 普通用户 | 优先 `ss -ltnp / -tnp`，兜底 `netstat` |
| 服务管理 | 启停可能 sudo | `systemctl list-units / show / status`、`journalctl -u …`；启停 / 启用 / 禁用均确认 |
| 计划任务 | 普通用户 | `crontab -l`、`cat /etc/crontab /etc/cron.d/*`、`systemctl list-timers`、`atq / at -c`，**不修改** |
| 命令历史 | 普通用户 | `cat ~/.bash_history ~/.zsh_history ~/.config/fish/fish_history` 等；不会修改 / 触发执行 |
| 安全审计 | 普通用户读 | `who / w / last / lastb`；`journalctl _COMM=sshd` 或 `/var/log/auth.log` |
| 软件与环境 | 普通用户读 | `dpkg -l / rpm -qa / apk info / pacman -Q`；`printenv`；按需 `ls $PATH` |
| 容器管理 | 容器组 / sudo | `docker ps -a / inspect / logs --tail 200`（podman 同理） |
| 防火墙管理 | root 或 sudo（规则采集需特权） | `iptables -t <tbl> -L -n -v --line-numbers`、`nft list ruleset`、`ufw status numbered`、`firewall-cmd --list-all-zones`、`fail2ban-client status [<jail>]`；控制操作（ufw add/delete、fail2ban unban、systemctl）均含二次确认 |
| 依赖检查 | 普通用户 | `command -v ss netstat ps systemctl docker …` 类型探测；不安装任何东西 |
| 资源告警轮询（`rss.alerts.enabled`） | 普通用户读 | 五项合一：`cat /proc/loadavg`、`grep … /proc/meminfo`、`grep '^cpu ' /proc/stat`、`df -Pk`、`nproc`；轮询间隔默认 30s；**默认关闭** |

> 服务管理 / 计划任务 / 安全审计 / 软件与环境 / 防火墙管理这 5 类只读采集，同时也通过 Copilot 工具（`#listServices` `#serviceLogs` `#listScheduledTasks` `#securityAudit` `#listPackages` `#firewallOverview`）暴露给 Copilot Chat，命令与上表完全一致；`#listPackages` / `#processDetail` 返回环境变量时会对疑似密码/密钥/令牌字段自动脱敏为 `***`。

---

## 远端依赖检查

VS Code 端不会向远端推送任何 binary，所以远端必须自带：`ss / netstat`、`ps`、`/proc`、`ip / ifconfig`、`tar / gzip` 等工具。常见的 minimal Alpine、容器镜像、定制 RHEL 经常缺失。

**入口**：
- 「已连接会话」面板标题栏 ✓ 图标
- 每个活动会话行右侧 ✓ inline 按钮
- 端口监控为空时点击「点此检查远端依赖组件 →」

**面板提供**：
- 操作系统指纹（PRETTY_NAME / 发行版族 / 内核 / 架构 / 是否 root）
- 9 类能力的探针结果（命令 + 详情）
- 按发行版自动选中推荐的安装命令（apt / yum / apk / pacman）+ 一键复制
- 「打开终端」按钮可立即在 SSH 终端中粘贴执行

---

## 配置项

所有设置位于 VS Code Settings → 搜索 `All in One SSH Studio`，或编辑 `settings.json`：

| 设置键 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `rss.storage.location` | enum (`global`\|`workspace`) | `global` | 连接配置文件存储位置；不会写入 `~/.ssh` |
| `rss.editor.tempDir` | string | `""` | 远程文件本地缓存目录；留空则使用扩展存储下的 `cache/` |
| `rss.sftp.concurrency` | number | `4` | SFTP 同时进行的并发任务数 |
| `rss.sftp.autoOpenExplorer` | boolean | `true` | 连接成功后自动打开 SFTP 资源管理器 |
| `rss.sftp.listCacheTtl` | number | `8` | SFTP 文件列表缓存有效期（秒，0 = 关闭） |
| `rss.sftp.deletePreStat` | boolean | `false` | 删除文件夹前是否递归统计子项数 / 大小用于确认弹窗 |
| `rss.sftp.deleteUseShellRm` | boolean | `false` | 删除文件夹时使用远端 shell 的 `rm -rf` 一次性删除（仅 1 次往返，远程大目录性能显著提升） |
| `rss.sftp.writeConfirm` | boolean | `false` | 编辑 `rss-sftp://` 在线文件时手动保存（Ctrl+S）前弹出确认，防止误操作覆盖远端文件 |
| `rss.terminal.defaultShell` | string | `""` | 远程终端启动时使用的 shell；留空使用服务器默认 |
| `rss.terminal.syncCwd` | boolean | `false` | 通过 OSC 7 把终端 cwd 同步到 SFTP 面板；默认关闭，可在 SFTP 面板中临时切换 |
| `rss.dashboard.refreshInterval` | number | `5` | 仪表盘自动刷新间隔（秒） |
| `rss.process.refreshInterval` | number | `3` | 进程管理器刷新间隔（秒） |
| `rss.network.refreshInterval` | number | `3` | 端口监控刷新间隔（秒） |
| `rss.transfer.maxConcurrent` | number | `4` | 传输队列最大并发数 |
| `rss.geoip.provider` | enum (`none`\|`ip-api`\|`ipinfo`\|`custom`) | `none` | 端口监控 GeoIP 提供方 |
| `rss.geoip.urlTemplate` | string | `""` | 自定义 GeoIP URL 模板，支持 `{ip}`、`{key}` 占位 |
| `rss.geoip.fieldMap` | object | `{country,region,city,org}` | 自定义 GeoIP 响应字段映射（点号路径） |
| `rss.audit.retentionDays` | number | `30` | 操作审计日志保留天数 |
| `rss.portForward.defaultListenAddr` | string | `127.0.0.1` | 新增本地端口转发的默认监听地址 |
| `rss.snippets.confirmRun` | boolean | `true` | 命令片段发送到终端前是否要求二次确认 |
| `rss.tools.<id>.enabled` | boolean | `true` | 控制工具集中各模块是否显示；14 个可配置 id：`snippets` / `portForward` / `logViewer` / `todos` / `process` / `port` / `history` / `services` / `schedules` / `software` / `containers` / `security` / `firewall` / `deps`；`sftp` 与 `transfer` 始终开启；设置变更即时生效，无需重载 |
| `rss.alerts.enabled` | boolean | `false` | 资源超阈告警主开关（默认关闭） |
| `rss.alerts.pollInterval` | number | `30` | 告警轮询间隔（秒，最小 10） |
| `rss.alerts.cooldownMinutes` | number | `15` | 同一连接同一指标的告警冷却时间（分钟） |
| `rss.alerts.consecutiveCount` | number | `2` | 连续超阈次数门槛，达到后才触发通知（防抖） |
| `rss.alerts.cpu.threshold` | number | `90` | CPU 使用率告警阈值（%） |
| `rss.alerts.memory.threshold` | number | `90` | 内存使用率告警阈值（%） |
| `rss.alerts.disk.threshold` | number | `85` | 磁盘使用率告警阈值（%，取所有挂载点中最高值） |
| `rss.alerts.load.threshold` | number | `2.0` | 系统负载阈值（1 分钟负载 / CPU 核数，默认 2.0×） |
| `rss.alerts.swap.threshold` | number | `50` | Swap 使用率告警阈值（%） |
| `rss.mcp.enabled` | boolean | `false` | 允许 Cursor、TRAE 等外部 MCP 客户端使用插件工具 |
| `rss.copilot.toolChannel` | `native` / `mcp` / `both` | `native` | 选择 VS Code Copilot 使用工具的方式；推荐 `native`，`both` 可能显示重复工具 |
| `rss.mcp.confirmationPolicy` | `riskBased` / `alwaysPrompt` | `riskBased` | 选择插件是为每次 MCP 调用追加确认，还是仅确认状态变更和破坏性操作 |

通过命令面板执行「All in One SSH Studio: 安全设置 GeoIP API Key…」来设置或清除 Key；值由 VS Code SecretStorage 保存，不会写入 `settings.json`。

### settings.json 示例

```jsonc
{
  "rss.dashboard.refreshInterval": 5,
  "rss.network.refreshInterval": 2,
  "rss.geoip.provider": "ip-api",
  "rss.terminal.syncCwd": false
}
```

---

## 命令面板速查

| 命令 | 用途 |
|---|---|
| `All in One SSH Studio: 显示欢迎页` | 重新打开欢迎引导 |
| `All in One SSH Studio: 新建 SSH 连接` | 快速添加服务器连接 |
| `All in One SSH Studio: 导入 / 导出连接配置` | 配置迁移 |
| `All in One SSH Studio: 打开终端` / `打开 SFTP 资源管理器` | 与已连接会话交互 |
| `All in One SSH Studio: 检查远端依赖组件` | 远端依赖体检 |
| `All in One SSH Studio: 复制 MCP 客户端配置` | 复制当前工作区供 Cursor、TRAE 或其他 MCP 客户端使用的 MCP 配置 |

---

## License

MIT
