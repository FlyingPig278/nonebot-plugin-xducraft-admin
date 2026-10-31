# nonebot-plugin-xducraft-admin

XDUCraft 网页管理面板插件，用于在浏览器中开关插件、修改各群设置和查看机器人运行状态。

运行整套机器人请看 [XDUCraft-bot](https://github.com/FlyingPig278/XDUCraft-bot)。

## 安装与加载

本软件包尚未发布至公共 PyPI 仓库，请通过 Git 标签安装。

仓库已内置编译生成的前端静态资源文件（位于 `src/xducraft_admin/static/`），安装与运行面板均无需安装 Node.js 或 npm。

### 1. 安装

在现有的 NoneBot 2.4.x 项目环境中，先安装 Core 核心包，再安装本插件：

```bash
pip install "nonebot-plugin-xducraft-core[onebot] @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-core.git@v0.3.0"
pip install "nonebot-plugin-xducraft-admin @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-admin.git@v0.1.0"
```

### 2. 加载配置

在 NoneBot 项目的 `pyproject.toml` 中将插件加载名追加到已有的 `plugins` 列表中（请保留已有插件与适配器配置，添加而非覆盖）：

```toml
[tool.nonebot]
plugins = [
    # 保留已有插件...
    "xducraft_config",
    "xducraft_admin",
]
```

## 配置与启动

在项目目录的 `.env` 中启用管理面板：

```ini
WEB_ADMIN_ENABLED=true
```

面板登录口令 `WEB_ADMIN_PASSWORD` 长度须至少 12 个字符。为了防止敏感口令在 NoneBot 全局配置中泄露，该口令必须直接设置在操作系统环境变量中，不支持从 `.env` 读取。如果未设置口令或口令不足 12 位，只有管理面板不会启动，机器人主进程与其他业务插件仍会正常运行。

在终端中设置口令并启动机器人：

Linux 与 macOS 环境：
```bash
export WEB_ADMIN_PASSWORD="your-strong-password-12+"
nb run
```

Windows PowerShell 环境：
```powershell
$env:WEB_ADMIN_PASSWORD="your-strong-password-12+"
nb run
```

面板默认监听本地端口 `8765`；如需修改，可在 `.env` 或操作系统环境变量中使用 `WEB_ADMIN_PORT` 指定其他端口。

## 访问与操作流程

服务启动后仅监听本地回环地址 `127.0.0.1:8765`（或自定义端口）。

### 1. 打开面板

- 本机部署：直接在浏览器访问 `http://127.0.0.1:8765/`。
- 远程服务器部署：通过 SSH 本地端口转发建立连接：
  ```bash
  ssh -N -L 8765:127.0.0.1:8765 user@your-server-ip
  ```
  随后在本地浏览器访问 `http://127.0.0.1:8765/`。

### 2. 功能页面与操作

登录页面输入 `WEB_ADMIN_PASSWORD` 中配置的口令即可进入管理界面：

- **插件**页面：集中管理各个插件的全局总开关与全局参数。所有业务插件默认处于关闭状态，群聊功能生效前必须先在此开启对应的全局总开关。
- **群聊**页面：
  - 展示机器人加入的群聊列表。若尚未连接 QQ 或尚未获取群信息，界面会提示“尚未获取群聊，请连接 QQ 后刷新”。
  - 支持通过“全部插件”与“本群已启用”筛选插件。
  - 选择具体群聊后，可开启该群的插件开关，并编辑保存群独立配置。
- **系统**页面：展示已连接机器人的在线状态与运行诊断信息。

## 本地开发与前端构建

### 后端开发与测试

仓库已包含预编译的静态资源。仅开发后端或执行测试时无需安装 Node.js：

```bash
pip install -e ".[dev]"
pytest
```

### 前端源码修改

前端源码位于 `webui/` 目录。修改前端界面时需要 Node.js 环境：

```bash
cd webui
npm ci
npm run build
cd ..
```

构建生成的产物位于 `src/xducraft_admin/static/`。修改前端后需将重新生成的静态资源与代码一同提交版本控制。

## 许可证与资源说明

本项目 Python 源码采用 [MIT License](LICENSE) 授权开源。

前端界面构建产物包含第三方 JavaScript 库、CSS 与图标资源，各第三方组件遵循其各自的开源许可协议，具体说明参见 [NOTICE](NOTICE) 文件与 [LICENSES/frontend-dependencies.txt](LICENSES/frontend-dependencies.txt)。
