# RustCom - 串口调试助手

**跨平台 | AI 可操控 | 嵌入式开发者的终极串口工具**

[![Version](https://img.shields.io/badge/version-0.6.1-blue.svg)]()
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20WSL%20%7C%20macOS-lightgrey.svg)]()

> 4.8MB 单文件，零依赖安装，开箱即用。

![RustCom 主界面](docs/screenshot.png)

---

## 下载

| 平台 | 下载 | 说明 |
|------|------|------|
| **Windows** | [rustcom.exe](https://github.com/suding-china/rustcom-release/releases/latest) | 双击运行，无需安装 |
| **Linux / WSL** | [rustcom](https://github.com/suding-china/rustcom-release/releases/latest) | 见下方安装说明 |

---

## 五大亮点

### 1. USB 设备映射（USBIPD）

**在 WSL 中直接使用 Windows 主机上的 USB 串口设备。**

RustCom 内置 USB/IP 设备管理器，一键将 Windows USB 设备映射到 WSL：

- 工具栏点击 **USB/IP** 按钮，自动列出可用 USB 设备
- 选择设备后一键 **Attach（挂载到 WSL）** 或 **Detach（卸载）**
- 无需手动在终端执行 `usbipd bind / attach` 命令
- 支持自动检测 `usbipd` 是否已安装

> **前提**：Windows 端需安装 [usbipd-win](https://github.com/dorssel/usbipd-win)，WSL 端需安装 USB 内核支持。

---

### 2. 多字符串快捷发送面板

**一次配置，反复使用——告别重复输入。**

- **20 条**可编辑的快捷发送按钮，点一下就发送
- 每条可自定义 **标签名称**（如"重启"、"查版本"、"读寄存器"）
- 支持 **文本 / HEX** 双模式，每条独立设置
- **序列发送**：按设定间隔逐条发送（自动化测试利器）
- **定时自动发送**：勾选后按间隔循环发送（压力测试）
- **统一换行**：一键为所有发送追加 CR/LF/CRLF
- **导入/导出**：从文本文件批量加载命令列表（每行一条）
- 右键菜单可插入/删除/移动行

> **打开方式**：工具栏右侧"多字符串 >>"按钮，或 `Ctrl+M`

---

### 3. 版本自动更新

**无需手动下载，应用内一键升级。**

- 菜单 **帮助 → 检查更新**，自动检测是否有新版本
- 发现新版本后显示更新说明，点击即可 **下载 + 安装 + 重启**
- Windows：自动备份旧版本，更新失败自动回退
- Linux/WSL：自动检测安装路径（支持 `~/.local/bin` 等）
- 更新过程有进度条提示，下载完成后一键替换

---

### 4. TCP 串口转发

**让局域网内的其他设备也能访问串口数据。**

- 工具栏 **TCP** 按钮一键开启，默认端口 9999
- 串口收到的数据实时广播给所有 TCP 客户端
- TCP 客户端发送的数据转发到串口
- 支持多客户端同时连接，状态栏显示连接数
- 每个标签页的 TCP 端口自动递增（9999, 10000, 10001...）

**典型用途**：
```bash
# 终端 1：实时查看串口日志
telnet 192.168.1.100 9999

# 终端 2：同时发送调试命令
telnet 192.168.1.100 9999
```

---

### 5. AI Agent 远程操控

**全球首个支持 AI 通过 TCP 全量操控的串口调试工具。**

RustCom 内置 **49 个 TCP 命令**，Claude Code 等 AI 工具可以：

- **自动发现能力**：`LIST_COMMANDS` 返回所有命令的 JSON 说明
- **读取串口日志**：`TAIL_RX 20` 查看最新数据，`GREP_RX ERROR` 过滤错误
- **AT 命令自动化**：`SEND_AND_WAIT AT+RST ||| OK 3000` 发送并等待响应
- **设备模拟**：`ADD_AUTO_REPLY AT ||| OK` 自动应答
- **完整控制**：连接/断开、参数设置、多会话管理、配置保存

```bash
# AI 四步自举流程
#CMD PING                    # 确认连通
#CMD LIST_COMMANDS            # 发现所有命令
#CMD LIST_KEYS                # 发现所有参数
#CMD DESCRIBE_UI              # 获取完整状态
```

**49 个 TCP 命令：**

| 类别 | 命令 |
|------|------|
| **基础** | HELP STATUS PORTS CONNECT DISCONNECT PING RESET |
| **收发** | SEND SEND_HEX CLEAR_RX CLEAR_TX PAUSE_RX RESUME_RX |
| **参数** | GET SET LIST_KEYS (33 GET + 25 SET 键) |
| **日志读取** | SEARCH GREP_RX GET_RX TAIL_RX |
| **多字符串** | SEND_MULTI SEND_MULTI_ALL SEQ_START SEQ_STOP GET/SET/LIST_MULTI |
| **自动化** | WAIT_FOR SEND_AND_WAIT ADD/LIST/DEL_AUTO_REPLY |
| **颜色规则** | ADD/LIST/DEL_COLOR_RULE |
| **会话管理** | LIST/SWITCH/NEW/CLOSE_SESSION |
| **文件** | SAVE_RX SAVE_RX_STOP SAVE_CONFIG |
| **元数据** | LIST_COMMANDS DESCRIBE_UI STATS SUBSCRIBE UNSUBSCRIBE |
| **安全** | AUTH (TOKEN 认证) · BATCH...END (批量执行) |

---

## 其他功能

### 串口通信
- 26 种波特率（含 ESP32 专用 74880）
- 自动波特率检测 · USB 热插拔自动发现
- 多标签多会话，每个会话完全独立

### 接收显示
- HEX / 文本 / Hex Dump 三种显示模式
- 关键字颜色高亮 + 自定义颜色规则 + ANSI 色
- 时间戳三种格式 · 搜索 + 行过滤 · 智能滚动
- 响应延迟实时测量（状态栏 ms 显示）

### 发送功能
- HEX 实时校验 · 转义序列 `\n \r \t \xNN`
- 发送历史 · 重发上次 · 文件发送

### 工具箱
- Modbus RTU 帧构造 + 帧解析
- 校验和计算器 · ASCII 码表 · 进制转换器

---

## 安装说明

### Windows

下载 `rustcom.exe`，双击运行。无需安装，无需管理员权限。

### WSL（Windows 子系统 Linux）

RustCom 可以在 WSL 中运行 GUI（需要 WSLg 支持，Windows 11 默认启用）：

```bash
# 1. 安装依赖
sudo apt update
sudo apt install -y libxcb-render0-dev libxcb-shape0-dev libxcb-xfixes0-dev \
  libxkbcommon-dev libgtk-3-dev libudev-dev

# 2. 下载 Linux 版本
wget -O rustcom https://github.com/suding-china/rustcom-release/releases/latest/download/rustcom
chmod +x rustcom

# 3. 安装到系统路径
sudo mv rustcom /usr/local/bin/

# 4. 运行
rustcom
```

**WSL 中使用 USB 串口设备**（需要 usbipd-win）：

```bash
# Windows 端（PowerShell 管理员）：
winget install usbipd

# Windows 端：共享 USB 设备
usbipd list                    # 查看设备
usbipd bind --busid 1-2       # 共享设备（首次）

# 之后在 RustCom 中点击 USB/IP 按钮即可一键挂载/卸载
```

### 原生 Linux

```bash
# 安装依赖
sudo apt install -y libudev-dev libxcb-render0-dev libxcb-shape0-dev \
  libxcb-xfixes0-dev libxkbcommon-dev libgtk-3-dev

# 下载并安装
wget -O rustcom https://github.com/suding-china/rustcom-release/releases/latest/download/rustcom
chmod +x rustcom
sudo mv rustcom /usr/local/bin/
rustcom
```

---

## 快捷键

| 快捷键 | 功能 | 快捷键 | 功能 |
|--------|------|--------|------|
| Ctrl+O | 打开/关闭串口 | Ctrl+T | 新建标签 |
| Ctrl+F | 搜索 | Ctrl+W | 关闭标签 |
| Ctrl+P | 暂停/继续 | Ctrl+Tab | 切换标签 |
| Ctrl+L | 清空接收 | Ctrl+M | 多字符串面板 |
| Ctrl+S | 保存配置 | Ctrl+= / - | 放大/缩小 |
| Ctrl+Q | 退出 | Ctrl+0 | 重置缩放 |
| Enter | 发送 | Ctrl+Enter | 换行 |
| Up/Down | 发送历史 | Esc | 关闭搜索/菜单 |

---

## 命令行参数

```
rustcom --port COM3 --baud 115200 --connect    启动即连接
rustcom --tcp-port 9999                        指定 TCP 端口
rustcom --tcp-token mytoken                    设置 TCP 认证密码
```

---

## 配置

配置自动保存在 `~/.rustcom/rustcom.conf`，支持多会话。

可通过菜单 **文件 → 导出/导入配置** 备份和迁移设置。

---

## 反馈与问题

如有问题或建议，请在 [Issues](https://github.com/suding-china/rustcom-release/issues) 中提交。
