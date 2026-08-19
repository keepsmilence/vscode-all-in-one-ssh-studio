# All in One SSH Studio

[English](README.md) | [简体中文](README_zh.md)

An all-in-one SSH client inside VS Code: connection management, SFTP browsing, remote editing, system/network/process monitoring, port forwarding, plus integration with GitHub Copilot Chat and external MCP clients.

> No binaries are pushed to the remote host, no telemetry is reported, and nothing is written to `~/.ssh`. By default, destructive operations require a second confirmation.

---

## Get Started in 30 Seconds

1. Click the **All in One SSH Studio** icon in the Activity Bar on the left → open the **Connections** panel → click **＋** in the title bar to create a new connection.
2. Fill in host / port / username / authentication (password · private key · SSH Agent), then click **Test Connection** to validate it (this does not write any configuration).
3. After validation succeeds, save and connect. Once connected, the **Dashboard** and **Toolbox** on the right become available automatically.

---

## Views and Entry Points

After opening **All in One SSH Studio** in the Activity Bar, you will see 5 panels:

| Panel | Purpose |
|---|---|
| **Connections** | Supports multi-level grouping, create / edit / connect / disconnect; connection configuration data can be imported and exported; context menus can copy the connection name for Copilot prompts, while **Disconnect** requires confirmation. For jump-host or private-network scenarios, configure the relay host as a normal connection, then select it under **Advanced → Jump Host (ProxyJump)** on the target connection; multi-hop nesting (A→B→C) is supported |
| **Active Sessions** | Current active sessions, with support for disconnecting (with confirmation) / creating a new SSH terminal / opening the SFTP browser / copying the session name; double-click a session to switch both Dashboard and Toolbox to it |
| **Dashboard** | Displays **CPU / Memory / Network / Disk / System Info** in sections inside a single panel; CPU and memory each have independent sections with 5-minute mini sparkline trends; progress bars change color by threshold (warn ≥75% / crit ≥90%) |
| **Toolbox** | Card-style unified entry point that organizes all feature panels below into two major groups (related to active sessions) |
| **Plugin Management** | Global tools entry point, decoupled from the session lifecycle: plugin usage guide / to-do management / operation audit log |

The first-run sidebar layout gives **Connections** and **Active Sessions** compact initial weights while prioritizing **Dashboard** and **Toolbox** space. VS Code preserves manual view resizing, so existing profiles keep their saved proportions.

### Toolbox · Resource Management

| Card | Description |
|---|---|
| **SFTP Browser** | Browse / upload / download / rename / delete; supports multi-select from the VS Code Explorer and drag into the current remote directory; stable draggable file-list columns persist as responsive proportions; multi-select (`Ctrl/Cmd` toggle, `Shift` range) + right-click bulk delete / bulk download; confirmed **one-click backup** for a single item (`.backup_YYYYMMDD` for files, `_backup_YYYYMMDD` for directories; existing same-day targets are not overwritten); delete confirmation + parent directory write-permission check; one-time decision for same-name conflicts in bulk operations (overwrite all / skip all / keep both for all / ask one by one); **right-click menu** (Enter Directory / Open in Terminal / Copy Path / Refresh, including blank areas); `rss.sftp.writeConfirm` can enable a confirmation before saving online files |
| **Command Snippets** | Global and connection-bound command templates; card-grid view + modal details / editing; shows global snippets and snippets for the current connection; actions: view details / edit / copy / fill into terminal / fill and execute / reset usage count / delete; dangerous commands are blocked by the whitelist |
| **Port Forwarding** | Supports both local→remote and remote→local modes; table shows status / current concurrency / cumulative upstream and downstream bytes; port conflicts are detected automatically; can inspect the remote process corresponding to the source port |
| **Log Viewer** | Bookmark remote log paths (supports template variables such as dates); **Realtime Mode** (`tail -F`, ring buffer, can pause / resume / clear) and **Paged Mode** (page by byte offset, no full-file scan required for large files); keyword search (`grep -bnF`) + `n/N` navigation; frontend syntax highlighting (timestamp / log level / JSON key / IP / URL); multi-tab support |
| **Transfer Queue** | View file upload/download progress, pause, and retry; when the panel is already open, new upload tasks refresh in the background without stealing focus from the SFTP tab; when the connection disconnects, queued tasks for that connection are canceled immediately, and running tasks stop automatically at the next checkpoint |
| **To-Do Tasks** | Global operations checklist for ops work; each item includes title / notes / priority / due time / tags / associated connection; supports status filtering and batch cleanup |

### Toolbox · System Management

| Card | Description |
|---|---|
| **Process Management** | List + sort indicators + filtering + 4 detail tabs (Summary / Environment Variables / File Descriptors / Kernel Stack); the details panel can be dragged taller; supports killing processes |
| **Port Monitor** | Dual-pane layout for listening ports and ESTABLISHED connections, with draggable resizing; columns are sortable, and double-click opens GeoIP details |
| **Command History** | Aggregates shell / REPL history for the current user (bash / zsh / fish / sh / python / node / mysql / psql / sqlite / redis), sorted by time descending; each row can be copied with one click or sent to the current terminal |
| **Service Management** | systemd service list (service name / status / startup enabled); click a row to tail the journal below; start / stop / restart / enable / disable all require confirmation |
| **Scheduled Tasks** | Three tabs: cron (including `/etc/crontab` + `cron.d` + each user's crontab), systemd timers, and at queue; clicking a row shows the triggered service / unit file / parsed cron fields |
| **Security Audit** | Five tabs: currently online (who/w), login history (`last`), failed attempts (`lastb`), sshd failure logs (`auth.log`/`journalctl`), and users & groups |
| **Software & Environment** | Four tabs: installed packages (with search), repository sources (apt/yum/apk/pacman), environment variables, and PATH commands (scanned on demand) |
| **Container Management** | Unified API for docker / podman; lists all containers (including stopped ones) + port mappings; details show state/ports/mounts/networks/env; copy the full container ID with one click, and open the mounted host directory directly in SFTP; Start/Stop/Restart plus tail 200 logs; compatible with podman output that emulates the docker CLI |
| **Firewall Management** | **Overview**: detect and display installation and runtime state for iptables / ip6tables / nftables / ufw / firewalld / Fail2Ban; supports service start/stop control. **iptables / ip6tables**: switch table (`filter`/`nat`/`mangle`/`raw`) and chain; rule table; high-risk rules (full-range ACCEPT) highlighted in red and medium-risk ones in orange. **nftables**: raw ruleset output with risk highlighting. **ufw**: numbered rule table + add / delete rule + enable / disable. **firewalld**: zone list + service control + permanent reload. **Fail2Ban**: jail status + banned IP list + one-click unban |
| **Resource Alerts** | Background polling of CPU / memory / disk / load / swap; when thresholds stay exceeded, a notification appears in the lower-right corner (disabled by default, enable via `rss.alerts.enabled`); triple suppression: consecutive trigger count · cooldown time · no more reminders for the day; notifications include a **View Dashboard** quick action |
| **Dependency Check** | One-click health check for 9 categories of required remote tools such as `ss`/`netstat`/`procps`/`iproute2`, with install commands suggested by distribution |

### Toolbox · Plugin Management

> Migrated: Plugin management has been moved out of the **Toolbox** and is now an independent top-level panel in the sidebar. See **Plugin Management** below.

### Plugin Management (Independent Sidebar Panel)

| Card | Description |
|---|---|
| **Plugin Usage Guide** | View the plugin usage guide and feature overview (the welcome page) |
| **Operation Audit Log** | Persists every terminal / panel / Copilot tool invocation in JSONL; 4+1 tabs (All / Terminal / Panel / Copilot / Errors); supports filtering by date / connection / keyword and hides structured dashboard / process / port / alert heartbeat records by default; realtime mode and JSONL export honor the active filters; supports clear and configurable retention |

---

## SSH Terminal

Integrated xterm; **Terminal Directory Sync** is enabled by default, so running `cd` in the terminal syncs the SFTP explorer directory via OSC 7. It can be disabled temporarily in the SFTP panel or globally in settings.

## Automatic Account Switching (Per Connection)

Enable sudo / su switching in the connection editor: after the interactive terminal starts, it injects `sudo -i -u` or `su - user`; the exec channel uses `printf base64 | base64 -d | sudo -n -H -u 'user' bash -s` wrapping, with no shell injection risk; all Copilot tool calls use the same privilege-escalation path.

## Remote Editing

Double-click a remote file to download it into a local cache, then save to write it back automatically. The cache path can be configured with `rss.editor.tempDir`; if left empty, `cache/` under extension storage is used.

---

## Copilot Chat Tools

By default, this extension integrates SSH/SFTP/system capabilities into GitHub Copilot Chat through VS Code native Language Model Tools. This native path still requires **no service or `.vscode/mcp.json` configuration**; VS Code handles tool discovery, authorization, and lifecycle management. Set `rss.copilot.toolChannel` to use the VS Code MCP provider instead. Its default, `native`, prevents duplicate capabilities in Copilot's tool list. An optional MCP compatibility path for other editors is described below.

### Available Tools

| Tool (`#` reference name) | Description |
|---|---|
| `#sshListConnections` | List SSH connections, nested group path, primary/alternate addresses, and user-provided internal/external network labels; returns structured expiration status and can proactively remind on approaching expiration (without sensitive fields) |
| `#sshExec` | Execute commands on a specified connection; commands matching the dangerous-command blacklist are rejected immediately; confirmation dialog is shown by default |
| `#interactiveStart` / `#interactiveRead` | Start a specific interactive administration tool in a controlled PTY and incrementally read menu / prompt output by cursor |
| `#interactiveSend` / `#interactiveClose` | After confirmation each time, send one line of non-sensitive input or terminate the interactive session; passwords / passphrases / OTPs are forcibly redirected to a real terminal |
| `#sftpList` / `#sftpRead` / `#sftpWrite` | Remote directory listing and text read/write (`>1MB` cannot be read; use `#sftpDownload` instead) |
| `#sftpUpload` / `#sftpDownload` | Upload / download a single file; uploads to protected paths are rejected |
| `#sftpUploadDir` | Recursively upload a local directory (requires confirmation); conflict policy is `overwrite` (default) or `skip` |
| `#systemInfo` | Collect `uname -a; uptime; free -h; df -h` |
| `#topProcesses` / `#findProcesses` / `#killProcess` | Find top processes by CPU/memory; match `ps args` by substring; KILL requires confirmation and enforces signal / PID allowlists |
| `#inspectPort` | Port listening status + ESTABLISHED connection count + deduplicated remote IP count + optional samples |
| `#tailLog` | One-shot `tail -n` snapshot (non-streaming), with optional `grep -F` filtering |
| `#listSnippets` / `#runSnippet` | List currently available snippets; fill into terminal with optional auto-enter (subject to dangerous-command allowlist and connection binding validation) |
| `#listPortForwards` | List port-forwarding configurations + realtime status (connection count / cumulative bytes / last error) |
| `#listServices` | List systemd/sysv services and their active/enabled status; supports name filtering and showing only running services |
| `#serviceLogs` | Read a `journalctl` log snapshot for a specified service (`unit`), with optional substring filtering |
| `#listScheduledTasks` | Summarize crontab / systemd timer / at queue; can optionally include details for a specific timer |
| `#firewallOverview` | Detect installation, runtime, and startup-enabled status for iptables/ip6tables/nftables/ufw/firewalld/fail2ban |
| `#listPackages` | Installed packages (optionally filtered by name) + repository sources; when `includeEnv` is enabled, environment variables are included with sensitive fields automatically masked as `***` |
| `#processDetail` | Details for a single PID such as command line / working directory / open files; when `includeEnv` is enabled, process environment variables are also included (and automatically masked in the same way) |
| `#securityAudit` | Currently online users, recent successful/failed logins, sshd authentication failure summaries, plus optional overviews of users/groups/sudoers |

> To-do management remains in the extension's **Toolbox**, but is no longer registered as a Copilot Chat tool, to avoid occupying tool-list and model-selection context.

### How to Use

1. Connect to the target host first in the **Connections** panel.
2. Open Copilot Chat, type `#`, choose `sshExec`, `sftpList`, and so on from the popup tool list; or describe your intent directly and let Copilot choose the tools.
3. Destructive operations are intercepted by VS Code's native second-confirmation UI.

### External MCP Clients

Cursor, TRAE, and other MCP clients can use the same complete set of SSH tools as GitHub Copilot Chat.

1. Enable `rss.mcp.enabled` in the extension settings.
2. In the same setting description, click **Copy this workspace's MCP client configuration**. If the link is unavailable in your editor, run **All in One SSH Studio: Copy MCP Client Configuration** from the Command Palette.
3. Paste the copied `mcpServers` object into the target client's MCP configuration and keep this extension window open while using the tools.
4. To make VS Code Copilot use MCP tools, set `rss.copilot.toolChannel` to `mcp`. Keep the default `native` mode to avoid duplicate tools; `both` may show the same tools twice.

With the default `rss.mcp.confirmationPolicy=riskBased`, approval for read-only non-destructive tools is left to Copilot, Cursor, or TRAE without an additional extension modal. State-changing or destructive tools such as command execution, writes, transfers, KILL, interactive input, and terminal snippets still require extension confirmation and show command, path, PID, and other operation details. Set the policy to `alwaysPrompt` to show extension confirmation for every call.

Advanced users can select the high-risk `clientApprovalOnly` policy. The extension shows a risk acknowledgement when this policy is first selected; after acceptance, it no longer confirms any MCP operation. Approval then depends entirely on the MCP client and its configuration, which may be configured to allow tools automatically. You accept responsibility for this risk. This policy does not bypass dangerous-command blocking, protected paths, workspace trust, input validation, SecretStorage, or auditing; those safeguards remain active, and Cursor, TRAE, or another client may still show its own approval prompt.

Every MCP invocation is audited with its tool, declared client, and target connection; input details pass through the same credential redaction and command/body truncation used by native tools. Passwords, private keys, and passphrases in SecretStorage never enter MCP results; approved remote output, file or log content, and process details are handled under the selected client and model provider's privacy terms.

After an extension reload, an existing configuration reconnects automatically when only one All in One SSH Studio window is providing MCP tools. If several workspace windows are active, copy the configuration again from the intended window.

### Deployment Example

```
Help me deploy seller-api using the prod-1 connection:
1. Run ./service.sh backup in /data/api/seller-api;
2. Upload the local /Users/me/work/seller-api/target/seller-api.jar to the remote path with the same name;
3. Recursively upload the local target/lib/ to overwrite the remote lib/;
4. Run ./service.sh restart;
5. Read the last 200 lines of log/seller-api.log and check whether there are startup errors.
```

Copilot will break it down as: `#sshExec backup` → `#sftpUpload jar` → `#sftpUploadDir lib` → `#sshExec restart` → `#tailLog log`. Each dangerous operation triggers VS Code's native confirmation dialog.

---

## Data Security and Privacy

### Where Your Data Is Stored

| Data Type | Storage Location |
|---|---|
| Host / port / username / groups / ProxyJump references | `connections.json` (VS Code `globalStorage`) — plain JSON text, readable only on the local machine, **never written to `~/.ssh`** |
| Password / private key content / passphrase / privilege-escalation password / GeoIP API key | **VS Code SecretStorage** (macOS Keychain · Windows Credential Vault · Linux libsecret) |
| Host fingerprints | `known_hosts.json` (also in `globalStorage`); written on first TOFU trust, and changes trigger an explicit dialog |
| Local cache of remote files | `cache/` subdirectory or `rss.editor.tempDir`, local only |
| Operation audit log | `audit/` in `globalStorage`, local JSONL with configurable retention; command bodies, LM file content, and interactive input are not persisted |
| MCP local service data | `mcp/` in `globalStorage`; contains local connection information and a rotating authorization token, with no SSH credentials |

### Outbound Network Traffic

- The extension itself **does not report any telemetry** and does not send any data to the author's servers.
- SSH / SFTP traffic goes **only** to the hosts you configure yourself (or through the jump hosts you configure yourself).
- **GeoIP resolution** is disabled by default. When enabled, only peer public IPs from the ESTABLISHED list are sent to the provider you choose; API keys are stored in SecretStorage.
- **External MCP access** is disabled by default, and VS Code Copilot defaults to the native tool channel. When MCP is enabled, communication between the extension and the MCP client stays on the local machine; the selected client and its model provider determine where approved tool results are sent.
- When you invoke a Copilot Chat tool, its result can include connection metadata, remote command output, file or log content, process details, and login records. VS Code passes that result to your selected language-model provider under that provider's privacy terms. The extension does not operate a separate relay or author-owned backend.

### Import / Export of Configuration

Use ☁⏫ / ☁⏬ in the title bar of the **Connections** panel to export / import; you can also search **Export Connection Configuration** / **Import Connection Configuration** in the Command Palette.

| Mode | Description |
|---|---|
| **Encrypted Export (Recommended)** | PBKDF2-SHA256 (200k iterations) key derivation + AES-256-GCM; importing requires the same passphrase; tampered files fail to decrypt |
| **Structure Only** | Exports connection metadata, group hierarchy, snippets, port-forward definitions, and log bookmarks **without credentials**. Review hostnames, usernames, notes, paths, and commands before sharing |
| **Plaintext Export** | Requires modal confirmation; for temporary local use only and should be deleted immediately afterwards |

When importing, all IDs are reassigned, and **existing connections are never overwritten**; ProxyJump references are rewritten based on the new mapping.

### Unified Protection Layer for SFTP / Copilot Operations

Write / delete / rename / upload / create folder operations all go through a unified path-protection layer. **No matter whether the source is the SFTP browser, the built-in VS Code file explorer (`rss-sftp://`), terminal OSC `rss-edit`, or Copilot Chat `#sftp*` tools**, the rules are the same:

- Protected root paths (exact match, only blocks the directory itself when used as the target): `/ /bin /sbin /boot /dev /lib /lib64 /proc /sys /usr /var /root /home /opt /run /srv /mnt /media`
- Protected subtree prefixes (if matched, any child file underneath is rejected): `/bin/ /sbin/ /boot/ /dev/ /lib/ /lib64/ /proc/ /sys/ /usr/ /root/`
  - By default, **`/etc/` is no longer included**. Editing service configuration such as `/etc/nginx/*.conf` and `/etc/systemd/**` is a legitimate ops scenario. If you want stricter protection, add `"/etc/"` to `rss.security.protectedSubtrees` in your settings.
- Sensitive filenames (even if the parent directory does not match a protected subtree, matching the basename still causes rejection): `.ssh/{authorized_keys,known_hosts,config,id_*}`, `sudoers(.d/*)`, `shadow`, `gshadow`, `passwd`, `crontab`

**Path normalization** handles special characters strictly: control characters are rejected (`NUL / CR / LF / TAB / DEL`); `//` is collapsed; `.` / `..` are resolved and prevented from escaping above root; under POSIX semantics, `\` is treated as a normal character rather than a separator. Invalid regex patterns are silently ignored and written to the output log, and will not crash the extension.

**Manual bypass**: when an interactive source (SFTP browser / file explorer / OSC `rss-edit`) hits a blocked path, a modal dialog appears with an **I understand, continue** button. Clicking it allows **only this single operation** and writes a warn-level audit log entry (including path, operation type, and connection name). If you do not want any bypass at all, set `rss.security.allowBypass` to `false`; then all blocks are hard stops. **Copilot Chat tool invocations are always hard-blocked and are not affected by `allowBypass`** to prevent AI from bypassing protections on its own.

Configurable items:

| Setting | Description |
|---|---|
| `rss.security.protectedPaths` | Exact-match list of protected root paths |
| `rss.security.protectedSubtrees` | List of subtree prefixes (entries must start and end with `/`; a missing trailing `/` is automatically added) |
| `rss.security.sensitiveFilePatterns` | Regex array for sensitive filenames (`i` mode) |
| `rss.security.interactiveCommandMode` | Controlled interactive strategy: `strict` (default, real-path allowlist) / `auto` (automatically allow trusted system programs) |
| `rss.security.interactiveAllowedCommands` | Optional real absolute paths of trusted remote executables in `strict` mode; ignored in `auto` mode |
| `rss.security.allowBypass` | Whether interactive sources may use the one-time **I understand, continue** bypass (default `true`) |

If `#sshExec` matches a dangerous command (`rm -rf /`, `mkfs`, `dd of=/dev/`, `shutdown`, firewall flushes, `crontab -r`, `curl|sh`, kernel parameter writes, and so on), it is rejected immediately. The error message instructs the user to run it manually in a real terminal with the appropriate identity (`sudo`). **The command-level dangerous-command allowlist never provides any bypass switch**.

### Controlled Interactive Sessions

`#interactiveStart` first resolves the real executable path through `command -v` + `realpath` / `readlink -f`. By default, `rss.security.interactiveCommandMode` is `strict`, which requires the result to match the optional path allowlist. When switched to `auto`, ordinary programs that are owned by root, not writable by group/others, and not located in user or temporary directories may be allowed automatically. Both modes reject system-level dangerous commands, shell syntax, credential parameters, as well as shells / interpreters / command wrappers / escape-capable programs. `#interactiveRead` reads incrementally by cursor and treats remote output as untrusted data; `#interactiveSend` shows the full one-line input before every send and requires confirmation, while also rejecting shell metacharacters and reusing the dangerous-command blacklist; `#interactiveClose` terminates the PTY and releases resources.

- Up to 2 active sessions per connection and 10 globally; an active session is terminated automatically after 15 minutes of inactivity, and output is retained for 5 minutes after termination.
- Output buffer limit is 256 KiB, and a single read returns at most 48 KiB; ANSI / OSC control sequences are removed before data is handed to the model.
- Common password, token, and API key output values are masked first; once prompts for password, passphrase, OTP, PIN, token, verification code, and similar inputs are detected, the session is blocked and terminated immediately, and the model must not continue sending input; a real SSH terminal must be used instead.
- The input body sent by `#interactiveSend` is not written to the audit log. Only length, connection, session, and whether Enter was sent are recorded, to avoid persisting secrets after accidental submission.

### Credential Best Practices

- Prefer **SSH Agent** or **private keys with a passphrase** over raw passwords.
- Put **jump hosts** in front of critical servers to reduce public exposure.
- Use **Test Connection** instead of connecting directly so you can validate authentication without writing `known_hosts`.

---

## What Commands Are Executed on the Remote Host?

This extension does not install any agent or download any binary to the remote host. All functionality is built on standard OpenSSH capabilities plus common Linux tools. The commands below are triggered by explicit user actions only, with **no silent background invocation**.

| Panel / Action | Required Privilege | Typical Commands |
|---|---|---|
| Establish SSH / SFTP | Regular user | Standard OpenSSH handshake; SFTP subsystem `ls/stat/open/read/write/mkdir/rename/remove` |
| Copilot controlled interaction | Depends on the confirmed command | Uses SSH PTY to execute the specific administration tool confirmed by the user; every round of non-sensitive input is confirmed separately; no general-purpose shell is exposed and no remote agent is installed |
| Terminal cwd sync (can be disabled) | Regular user | `PROMPT_COMMAND` / `precmd` injects OSC 7 |
| Dashboard · System / Network | Regular user read | `uname / uptime`, `/proc/{loadavg,meminfo,cpuinfo,net/dev}`, `df -P`, fallback `ip -s link` and `ifconfig` |
| Process Management | Regular user read · KILL requires corresponding privilege | `ps -eo …`, `/proc/<pid>/{status,environ,fd,stack}`; KILL always requires explicit confirmation |
| Port Monitor | Regular user | Prefer `ss -ltnp / -tnp`, fallback `netstat` |
| Service Management | Start/stop may require sudo | `systemctl list-units / show / status`, `journalctl -u …`; start/stop / enable / disable all require confirmation |
| Scheduled Tasks | Regular user | `crontab -l`, `cat /etc/crontab /etc/cron.d/*`, `systemctl list-timers`, `atq / at -c`, **no modifications** |
| Command History | Regular user | `cat ~/.bash_history ~/.zsh_history ~/.config/fish/fish_history` and similar; never modifies or triggers execution |
| Security Audit | Regular user read | `who / w / last / lastb`; `journalctl _COMM=sshd` or `/var/log/auth.log` |
| Software & Environment | Regular user read | `dpkg -l / rpm -qa / apk info / pacman -Q`; `printenv`; `ls $PATH` on demand |
| Container Management | Container group / sudo | `docker ps -a / inspect / logs --tail 200` (same idea for podman) |
| Firewall Management | root or sudo (privilege required to collect rules) | `iptables -t <tbl> -L -n -v --line-numbers`, `nft list ruleset`, `ufw status numbered`, `firewall-cmd --list-all-zones`, `fail2ban-client status [<jail>]`; control actions (`ufw add/delete`, `fail2ban unban`, `systemctl`) all require confirmation |
| Dependency Check | Regular user | `command -v ss netstat ps systemctl docker …` capability probes; does not install anything |
| Resource alert polling (`rss.alerts.enabled`) | Regular user read | Five-in-one: `cat /proc/loadavg`, `grep … /proc/meminfo`, `grep '^cpu ' /proc/stat`, `df -Pk`, `nproc`; default polling interval is 30s; **disabled by default** |

> The five read-only collection categories Service Management / Scheduled Tasks / Security Audit / Software & Environment / Firewall Management are also exposed to Copilot Chat through the tools `#listServices` `#serviceLogs` `#listScheduledTasks` `#securityAudit` `#listPackages` `#firewallOverview`, using exactly the same commands as shown above; when `#listPackages` / `#processDetail` return environment variables, suspected password/key/token fields are automatically masked as `***`.

---

## Remote Dependency Check

The VS Code side never pushes any binary to the remote host, so the remote host must already provide tools such as `ss / netstat`, `ps`, `/proc`, `ip / ifconfig`, and `tar / gzip`. Common minimal Alpine images, container images, and customized RHEL systems often miss some of them.

**Entry points**:
- ✓ icon in the title bar of the **Connected Sessions** panel
- ✓ inline button on the right side of each active session row
- Click **Click here to check remote dependencies →** when Port Monitor is empty

**What the panel provides**:
- OS fingerprint (`PRETTY_NAME` / distro family / kernel / architecture / whether root)
- Probe results for 9 capability categories (command + details)
- Recommended install command automatically selected by distribution (`apt` / `yum` / `apk` / `pacman`) + one-click copy
- **Open Terminal** button to paste and run immediately in the SSH terminal

---

## Settings

All settings are available in VS Code Settings → search for **All in One SSH Studio**, or edit `settings.json` directly:

| Setting Key | Type | Default | Description |
|---|---|---|---|
| `rss.displayLanguage` | `auto` / `zh-cn` / `en` | `auto` | Language for extension-owned UI and runtime messages; reload the window after changing it. Sidebar view titles switch after activation; command, menu, and native Settings titles still follow the editor language; external MCP tool metadata remains English |
| `rss.storage.location` | enum (`global`\|`workspace`) | `global` | Storage location of the connection configuration file; never written to `~/.ssh` |
| `rss.editor.tempDir` | string | `""` | Local cache directory for remote files; if empty, `cache/` under extension storage is used |
| `rss.sftp.concurrency` | number | `4` | Number of concurrent SFTP tasks |
| `rss.sftp.autoOpenExplorer` | boolean | `true` | Automatically open the SFTP explorer after a successful connection |
| `rss.sftp.listCacheTtl` | number | `8` | TTL of the SFTP file-list cache (seconds, `0 = disabled`) |
| `rss.sftp.deletePreStat` | boolean | `false` | Whether to recursively count child items / size before deleting a folder for the confirmation dialog |
| `rss.sftp.deleteUseShellRm` | boolean | `false` | When deleting a folder, use the remote shell command `rm -rf` to remove it in one shot (only one round trip, significantly faster for large remote directories) |
| `rss.sftp.writeConfirm` | boolean | `false` | When editing an online file via `rss-sftp://`, show a confirmation before manual save (`Ctrl+S`) to avoid accidentally overwriting the remote file |
| `rss.terminal.defaultShell` | string | `""` | Shell used when starting the remote terminal; if empty, the server default is used |
| `rss.terminal.syncCwd` | boolean | `true` | Sync terminal cwd to the SFTP panel via OSC 7; enabled by default and can be toggled temporarily in the SFTP panel |
| `rss.dashboard.refreshInterval` | number | `5` | Auto-refresh interval of the dashboard (seconds) |
| `rss.process.refreshInterval` | number | `3` | Refresh interval of Process Management (seconds) |
| `rss.network.refreshInterval` | number | `3` | Refresh interval of Port Monitor (seconds) |
| `rss.transfer.maxConcurrent` | number | `4` | Maximum concurrency of the transfer queue |
| `rss.geoip.provider` | enum (`none`\|`ip-api`\|`ipinfo`\|`custom`) | `none` | GeoIP provider for Port Monitor |
| `rss.geoip.urlTemplate` | string | `""` | Custom GeoIP URL template with `{ip}` and `{key}` placeholders |
| `rss.geoip.fieldMap` | object | `{country,region,city,org}` | Mapping of custom GeoIP response fields (dot-path syntax) |
| `rss.audit.retentionDays` | number | `30` | Retention period of the operation audit log in days |
| `rss.portForward.defaultListenAddr` | string | `127.0.0.1` | Default listen address for new local port forwarding |
| `rss.snippets.confirmRun` | boolean | `true` | Whether command snippets require confirmation before being sent to the terminal |
| `rss.tools.<id>.enabled` | boolean | `true` | Controls whether each module is shown in the Toolbox; 14 configurable ids: `snippets` / `portForward` / `logViewer` / `todos` / `process` / `port` / `history` / `services` / `schedules` / `software` / `containers` / `security` / `firewall` / `deps`; `sftp` and `transfer` are always enabled; changes take effect immediately with no reload required |
| `rss.alerts.enabled` | boolean | `false` | Master switch for resource-threshold alerts (disabled by default) |
| `rss.alerts.pollInterval` | number | `30` | Alert polling interval in seconds (minimum `10`) |
| `rss.alerts.cooldownMinutes` | number | `15` | Alert cooldown for the same metric on the same connection (minutes) |
| `rss.alerts.consecutiveCount` | number | `2` | Number of consecutive threshold breaches required before a notification is triggered (debounce) |
| `rss.alerts.cpu.threshold` | number | `90` | CPU usage alert threshold (%) |
| `rss.alerts.memory.threshold` | number | `90` | Memory usage alert threshold (%) |
| `rss.alerts.disk.threshold` | number | `85` | Disk usage alert threshold (%) using the highest value across all mount points |
| `rss.alerts.load.threshold` | number | `2.0` | System load threshold (1-minute load / CPU core count, default 2.0×) |
| `rss.alerts.swap.threshold` | number | `50` | Swap usage alert threshold (%) |
| `rss.mcp.enabled` | boolean | `false` | Allow external MCP clients such as Cursor and TRAE to use the extension's tools |
| `rss.copilot.toolChannel` | `native` / `mcp` / `both` | `native` | Select how VS Code Copilot accesses the tools; `native` is recommended, while `both` may show duplicate tools |
| `rss.mcp.confirmationPolicy` | `riskBased` / `alwaysPrompt` / `clientApprovalOnly` | `riskBased` | Control extension-level MCP confirmation; `clientApprovalOnly` disables it entirely and relies on client configuration at your own risk |

Set or clear a GeoIP API key with **All in One SSH Studio: Securely Set GeoIP API Key…** in the Command Palette. The value is stored in VS Code SecretStorage and is not written to `settings.json`.

### `settings.json` Example

```jsonc
{
  "rss.displayLanguage": "auto",
  "rss.dashboard.refreshInterval": 5,
  "rss.network.refreshInterval": 2,
  "rss.geoip.provider": "ip-api",
  "rss.terminal.syncCwd": true
}
```

---

## Command Palette Quick Reference

| Command | Purpose |
|---|---|
| `All in One SSH Studio: Show Welcome Page` | Reopen the welcome guide |
| `All in One SSH Studio: New SSH Connection` | Quickly add a server connection |
| `All in One SSH Studio: Import / Export Connection Configuration` | Configuration migration |
| `All in One SSH Studio: Open Terminal` / `Open SFTP Explorer` | Interact with connected sessions |
| `All in One SSH Studio: Check Remote Dependencies` | Remote dependency health check |
| `All in One SSH Studio: Copy MCP Client Configuration` | Copy the MCP configuration for this workspace for use in Cursor, TRAE, or another MCP client |

---

## License

MIT