# AI-Trader Linux 系统安装指南

本指南将帮助您在 Linux 系统上从零开始安装和配置 AI-Trader 项目。

## 📋 目录

1. [系统要求](#系统要求)
2. [安装步骤](#安装步骤)
3. [环境配置](#环境配置)
4. [数据准备](#数据准备)
5. [启动服务](#启动服务)
6. [验证安装](#验证安装)
7. [常见问题](#常见问题)

---

## 系统要求

### 最低配置

- **操作系统**: Linux (Ubuntu 18.04+, Debian 10+, CentOS 7+, 或其他主流发行版)
- **Python**: 3.10 或更高版本
- **内存**: 至少 4GB RAM
- **磁盘空间**: 至少 5GB 可用空间
- **网络**: 稳定的互联网连接（用于 API 调用和数据获取）

### 推荐配置

- **操作系统**: Ubuntu 22.04 LTS 或更新版本
- **Python**: 3.11+
- **内存**: 8GB RAM 或更多
- **磁盘空间**: 10GB 或更多
- **CPU**: 多核处理器（推荐 4 核或以上）

---

## 安装步骤

### 步骤 1: 检查和安装 Python

首先，检查您的系统是否已安装 Python 3.10+：

```bash
python3 --version
```

如果版本低于 3.10 或未安装，请根据您的 Linux 发行版安装：

#### Ubuntu/Debian

```bash
# 更新软件包列表
sudo apt update

# 安装 Python 3.11
sudo apt install python3.11 python3.11-venv python3.11-dev python3-pip -y

# 设置 Python 3.11 为默认版本（可选）
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 1
```

#### CentOS/RHEL/Fedora

```bash
# 安装 EPEL 仓库（CentOS/RHEL）
sudo yum install epel-release -y

# 安装 Python 3.11
sudo yum install python311 python311-devel python311-pip -y
```

#### Arch Linux

```bash
sudo pacman -S python python-pip
```

### 步骤 2: 安装必要的系统依赖

```bash
# Ubuntu/Debian
sudo apt install git curl wget build-essential libssl-dev libffi-dev -y

# CentOS/RHEL
sudo yum install git curl wget gcc gcc-c++ make openssl-devel libffi-devel -y

# Arch Linux
sudo pacman -S git curl wget base-devel openssl
```

### 步骤 3: 克隆项目仓库

```bash
# 进入您想要安装项目的目录
cd ~

# 克隆项目
git clone https://github.com/HKUDS/AI-Trader.git

# 进入项目目录
cd AI-Trader
```

### 步骤 4: 创建 Python 虚拟环境（推荐）

使用虚拟环境可以避免依赖冲突：

```bash
# 创建虚拟环境
python3 -m venv venv

# 激活虚拟环境
source venv/bin/activate

# 验证虚拟环境
which python
# 应该显示: /home/你的用户名/AI-Trader/venv/bin/python
```

**重要**: 每次使用项目前，都需要激活虚拟环境：
```bash
cd ~/AI-Trader
source venv/bin/activate
```

### 步骤 5: 升级 pip 并安装依赖

```bash
# 升级 pip 到最新版本
pip install --upgrade pip

# 安装项目依赖
pip install -r requirements.txt
```

如果遇到安装问题，可以逐个安装核心依赖：

```bash
pip install langchain==1.0.2
pip install langchain-openai==1.0.1
pip install langchain-mcp-adapters>=0.1.0
pip install fastmcp==2.12.5
pip install tushare
```

---

## 环境配置

### 步骤 1: 创建环境变量文件

```bash
# 复制示例配置文件
cp .env.example .env

# 使用您喜欢的编辑器编辑 .env 文件
nano .env
# 或者
vim .env
```

### 步骤 2: 配置 API 密钥

在 `.env` 文件中填入以下信息：

```bash
# AI 模型 API 配置
OPENAI_API_BASE="https://api.openai.com/v1"  # OpenAI API 地址
OPENAI_API_KEY="sk-your-openai-api-key"      # 您的 OpenAI API Key

# 数据源 API 配置
ALPHAADVANTAGE_API_KEY="your-alpha-vantage-key"  # Alpha Vantage API Key（美股数据）
JINA_API_KEY="your-jina-api-key"                 # Jina AI API Key（信息搜索）
TUSHARE_TOKEN="your-tushare-token"               # Tushare Token（A股数据，可选）

# 服务端口配置（默认即可，如有冲突可修改）
MATH_HTTP_PORT=8000
SEARCH_HTTP_PORT=8001
TRADE_HTTP_PORT=8002
GETPRICE_HTTP_PORT=8003

# AI 代理配置
AGENT_MAX_STEP=30

# 运行环境配置文件路径（推荐使用绝对路径）
RUNTIME_ENV_PATH=/home/你的用户名/AI-Trader/runtime_env.json
```

### 步骤 3: 获取 API 密钥

#### OpenAI API Key
1. 访问 [OpenAI Platform](https://platform.openai.com/)
2. 注册/登录账户
3. 进入 "API Keys" 页面
4. 创建新的 API Key

#### Alpha Vantage API Key（美股数据）
1. 访问 [Alpha Vantage](https://www.alphavantage.co/)
2. 点击 "Get Your Free API Key Today"
3. 填写信息后获取免费 API Key

#### Jina AI API Key
1. 访问 [Jina AI](https://jina.ai/)
2. 注册账户并获取 API Key

#### Tushare Token（A股数据，可选）
1. 访问 [Tushare Pro](https://tushare.pro/)
2. 注册账户
3. 在个人中心获取 Token

### 步骤 4: 检查端口可用性

确保配置的端口未被占用：

```bash
# 检查端口是否被占用
netstat -tuln | grep -E '8000|8001|8002|8003'

# 或使用 ss 命令
ss -tuln | grep -E '8000|8001|8002|8003'
```

如果端口被占用，请在 `.env` 文件中修改为其他可用端口。

---

## 数据准备

### 选项 1: 交易美股（纳斯达克100）

#### 步骤 1: 获取美股数据

```bash
# 确保虚拟环境已激活
source venv/bin/activate

# 进入数据目录
cd data

# 运行数据获取脚本
python get_daily_price.py

# 这将下载纳斯达克100成分股的历史价格数据
# 根据网络速度，可能需要 10-30 分钟
```

#### 步骤 2: 合并数据

```bash
# 在 data 目录中运行
python merge_jsonl.py

# 这将生成 merged.jsonl 文件
```

#### 步骤 3: 返回项目根目录

```bash
cd ..
```

### 选项 2: 交易 A 股（上证50）

#### 步骤 1: 获取 A 股数据

```bash
# 确保虚拟环境已激活
source venv/bin/activate

# 进入 A 股数据目录
cd data/A_stock

# 运行 A 股数据获取脚本
python get_daily_price_a_stock.py

# 需要有效的 Tushare Token
```

#### 步骤 2: 转换数据格式

```bash
# 在 data/A_stock 目录中运行
python merge_a_stock_jsonl.py

# 这将生成 data/A_stock/merged.jsonl 文件
```

#### 步骤 3: 返回项目根目录

```bash
cd ../..
```

### 使用脚本自动准备数据（推荐）

项目提供了便捷的启动脚本：

#### 美股数据准备

```bash
bash scripts/main_step1.sh
```

#### A 股数据准备

```bash
bash scripts/main_a_stock_step1.sh
```

---

## 启动服务

### 方法 1: 使用脚本启动（推荐）

#### 运行美股交易

```bash
# 确保在项目根目录并激活虚拟环境
cd ~/AI-Trader
source venv/bin/activate

# 方式 1: 一键启动（包含数据准备、启动服务、运行交易）
bash scripts/main.sh

# 方式 2: 分步启动
bash scripts/main_step1.sh  # 准备数据
bash scripts/main_step2.sh  # 启动 MCP 服务
bash scripts/main_step3.sh  # 运行交易代理
```

#### 运行 A 股交易

```bash
# 分步启动
bash scripts/main_a_stock_step1.sh  # 准备 A 股数据
bash scripts/main_a_stock_step2.sh  # 启动 MCP 服务
bash scripts/main_a_stock_step3.sh  # 运行 A 股交易代理
```

### 方法 2: 手动启动

#### 步骤 1: 启动 MCP 工具服务

```bash
# 确保虚拟环境已激活
source venv/bin/activate

# 进入工具目录
cd agent_tools

# 启动 MCP 服务（在后台运行）
python start_mcp_services.py &

# 记录进程 ID 以便后续关闭
echo $! > mcp_services.pid

# 返回项目根目录
cd ..
```

#### 步骤 2: 运行交易代理

**美股交易**:
```bash
# 使用默认配置
python main.py

# 或指定配置文件
python main.py configs/default_config.json
```

**A 股交易**:
```bash
# 使用 A 股配置
python main.py configs/astock_config.json
```

### 启动 Web 界面

```bash
# 方法 1: 使用脚本
bash scripts/start_ui.sh

# 方法 2: 手动启动
cd docs
python3 -m http.server 8888

# 浏览器访问: http://localhost:8888
```

---

## 验证安装

### 检查依赖安装

```bash
# 激活虚拟环境
source venv/bin/activate

# 检查已安装的包
pip list | grep -E 'langchain|fastmcp|tushare'
```

预期输出应包含：
```
fastmcp              2.12.5
langchain            1.0.2
langchain-mcp-adapters  0.1.0
langchain-openai     1.0.1
tushare              1.x.x
```

### 检查数据文件

```bash
# 检查美股数据
ls -lh data/merged.jsonl

# 检查 A 股数据
ls -lh data/A_stock/merged.jsonl
```

### 检查 MCP 服务状态

```bash
# 检查服务端口是否监听
netstat -tuln | grep -E '8000|8001|8002|8003'

# 或使用 ss
ss -tuln | grep -E '8000|8001|8002|8003'
```

### 测试运行

```bash
# 简单测试：运行一次交易（使用较短的日期范围）
# 编辑配置文件，设置较短的测试日期范围
python main.py configs/default_config.json
```

---

## 常见问题

### Q1: Python 版本不符合要求

**问题**: 系统 Python 版本低于 3.10

**解决方案**:
```bash
# 使用 pyenv 管理多个 Python 版本
curl https://pyenv.run | bash

# 添加到 shell 配置文件 (~/.bashrc 或 ~/.zshrc)
echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc

# 安装 Python 3.11
pyenv install 3.11.0
pyenv local 3.11.0
```

### Q2: pip 安装依赖失败

**问题**: 安装 requirements.txt 时出错

**解决方案**:
```bash
# 升级 pip 和 setuptools
pip install --upgrade pip setuptools wheel

# 清除 pip 缓存
pip cache purge

# 重新安装
pip install -r requirements.txt --no-cache-dir
```

### Q3: API 连接超时

**问题**: 获取数据时提示连接超时或 API 错误

**解决方案**:
1. 检查网络连接是否正常
2. 验证 API Key 是否正确
3. 检查 API 配额是否用尽
4. 如果在中国大陆，可能需要配置代理：
```bash
# 设置 HTTP 代理（临时）
export http_proxy=http://your-proxy:port
export https_proxy=http://your-proxy:port

# 或在 Python 代码中配置
```

### Q4: 端口被占用

**问题**: MCP 服务启动失败，提示端口被占用

**解决方案**:
```bash
# 查找占用端口的进程
sudo lsof -i :8000

# 终止占用进程
sudo kill -9 <PID>

# 或修改 .env 文件中的端口配置
```

### Q5: 数据文件不存在

**问题**: 运行时提示找不到数据文件

**解决方案**:
```bash
# 确保已运行数据准备脚本
bash scripts/main_step1.sh  # 美股
# 或
bash scripts/main_a_stock_step1.sh  # A股

# 检查数据文件是否存在
ls -lh data/merged.jsonl
ls -lh data/A_stock/merged.jsonl
```

### Q6: 权限问题

**问题**: 脚本执行提示权限不足

**解决方案**:
```bash
# 给脚本添加执行权限
chmod +x scripts/*.sh

# 如果是文件读写权限问题
sudo chown -R $USER:$USER ~/AI-Trader
```

### Q7: 虚拟环境激活失败

**问题**: `source venv/bin/activate` 不起作用

**解决方案**:
```bash
# 确保虚拟环境已创建
python3 -m venv venv

# 如果使用 fish shell
source venv/bin/activate.fish

# 如果使用 csh/tcsh
source venv/bin/activate.csh
```

### Q8: 内存不足

**问题**: 运行时系统卡顿或提示内存不足

**解决方案**:
```bash
# 检查可用内存
free -h

# 增加交换空间（临时解决）
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 或减少并发运行的 AI 模型数量
# 编辑配置文件，禁用部分模型
```

### Q9: 如何关闭后台服务

**问题**: 如何正确停止 MCP 服务

**解决方案**:
```bash
# 如果记录了 PID
kill $(cat agent_tools/mcp_services.pid)

# 或查找并终止进程
ps aux | grep start_mcp_services
kill -9 <PID>

# 检查端口是否已释放
netstat -tuln | grep -E '8000|8001|8002|8003'
```

### Q10: Alpha Vantage API 限制

**问题**: Alpha Vantage 免费版 API 有每分钟 5 次调用限制

**解决方案**:
- 免费版足够日常使用，但获取完整历史数据需要较长时间
- 可以在脚本中添加延迟（已内置）
- 或考虑升级到高级版 API
- 使用已下载的数据进行测试

---

## 性能优化建议

### 1. 使用 SSD 存储

数据文件读写频繁，建议将项目放在 SSD 上。

### 2. 调整并发数

根据系统配置调整同时运行的 AI 模型数量：

```json
// configs/default_config.json
{
  "models": [
    {
      "name": "claude-3.7-sonnet",
      "enabled": true  // 系统资源有限时，禁用部分模型
    }
  ]
}
```

### 3. 使用本地缓存

项目会自动缓存 API 响应，确保有足够的磁盘空间。

### 4. 监控资源使用

```bash
# 实时监控系统资源
htop

# 或使用
top
```

---

## 下一步

安装完成后，您可以：

1. 阅读 [README_CN.md](README_CN.md) 了解详细功能
2. 查看 [configs/](configs/) 目录自定义配置
3. 访问 Web 界面查看交易结果
4. 修改配置文件尝试不同的 AI 模型和交易策略

---

## 获取帮助

如果遇到问题：

- 查看 [GitHub Issues](https://github.com/HKUDS/AI-Trader/issues)
- 参与 [GitHub Discussions](https://github.com/HKUDS/AI-Trader/discussions)
- 查看项目文档和示例

---

## 卸载

如需卸载项目：

```bash
# 停止所有服务
pkill -f start_mcp_services
pkill -f main.py

# 删除虚拟环境
rm -rf venv

# 删除项目目录（可选）
cd ~
rm -rf AI-Trader
```

---

**祝您使用愉快！如果本指南对您有帮助，欢迎给项目一个 Star！**
