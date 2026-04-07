# RoboClaw 运行指南：本地环境配置与初始化

要让 RoboClaw 框架在本地（纯软件沙盒模式或连接真机）顺利跑起来，你需要完成代码拉取、环境依赖安装以及关键的 API 密钥配置。以下是保姆级的搭建步骤：

## 一、 前置条件 (Prerequisites)

在开始之前，请确保你的电脑上已经安装了以下基础软件：
* **Git**: 用于拉取项目代码。
* **Python**: 建议安装 Python 3.10 或以上版本（项目根目录有 `.python-version` 文件指定了具体版本）。
* **Make**: 用于执行 `Makefile` 中的快捷命令（Mac/Linux 通常自带，Windows 用户可通过 WSL 或安装 Make for Windows 获取）。

---

## 二、 核心搭建步骤

### 1. 克隆项目仓库
首先，将 RoboClaw 的官方仓库克隆到本地机器上。打开你的终端（Terminal），执行：

```bash
git clone [https://github.com/RoboClaw-Robotics/RoboClaw.git](https://github.com/RoboClaw-Robotics/RoboClaw.git) ~/RoboClaw
cd ~/RoboClaw
```

### 2. 配置环境变量 (.env)
RoboClaw 的 VLM 大脑推理和人类兜底机制严重依赖外部 API。系统通过读取 `.env` 文件来获取这些密钥。

**第一步**：从项目提供的模板复制一份配置文件：
```bash
cp .env.example .env
```

**第二步**：使用文本编辑器（如 VSCode 或 vim）打开 `.env` 文件，填入你的专属凭证。文件内容通常如下：

```ini
# --- 核心大脑配置 (必须) ---
# 用于驱动 VLM 进行高阶推理和动作规划
OPENAI_API_KEY=sk-your-openai-api-key-here

# --- 人工兜底机制/飞书通知配置 (可选) ---
# 当机器人遇到无法恢复的物理故障或目标丢失时，通过飞书向人类工程师报警
FEISHU_APP_ID=cli_xxx
FEISHU_APP_SECRET=xxx
FEISHU_VERIFICATION_TOKEN=xxx
FEISHU_EVENT_RECEIVER=long_connection  # 启用长连接模式监听飞书指令
```
> **💡 学习笔记提示**：如果你只是想在本地运行 TUI 沙盒模式体验大模型的逻辑，飞书的配置项（`FEISHU_*`）可以先放空，但 `OPENAI_API_KEY` 是必须填写的。

### 3. 安装依赖与初始化环境
仔细观察项目源码可以发现，RoboClaw 使用了当下 Python 生态中最先进、极速的包管理器 **`uv`**（标志是项目里有 `uv.lock` 和 `pyproject.toml` 文件）。

但你不需要手动去折腾虚拟环境，开发者已经在 `Makefile` 里写好了自动化脚本。只需在项目根目录运行：

```bash
make init
```
* **这个命令在后台做了什么？** 它会自动帮你创建隔离的 Python 虚拟环境，并根据 `uv.lock` 文件，以毫秒级的速度精确安装所有的底层驱动和算法依赖库。

---

## 三、 验证与启动

当环境初始化完成后，你可以通过以下两个命令来验证框架是否搭建成功：

### 模式 A：启动虚拟终端交互 (TUI - 推荐学习使用)
如果你没有智元 G01 机器狗等真实硬件，请使用此模式。它将启动一个纯文本的交互式沙盒环境。
```bash
make run_tui
```
在这里，你可以通过打字输入任务指令，并“扮演”摄像头给 VLM 文本反馈，观察智能体大脑的思考与动作分发逻辑。

### 模式 B：启动图形化监控界面 (GUI)
如果你需要更直观地监控状态机或者连接了实体硬件，可以启动图形界面版：
```bash
make run_gui
```
