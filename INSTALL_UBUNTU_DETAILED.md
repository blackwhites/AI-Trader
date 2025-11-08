# AI-Trader Ubuntu 系统详细安装指南

本指南专门针对 Ubuntu 系统（Ubuntu 20.04/22.04/24.04），提供最详细的安装步骤。

## 📋 目录

- [系统准备](#系统准备)
- [Python 环境安装](#python-环境安装)
- [项目安装](#项目安装)
- [API 密钥获取](#api-密钥获取)
- [环境配置](#环境配置)
- [数据准备](#数据准备)
- [服务启动](#服务启动)
- [验证测试](#验证测试)
- [常见问题详解](#常见问题详解)
- [完整示例流程](#完整示例流程)

---

## 系统准备

### 检查 Ubuntu 版本

```bash
# 查看 Ubuntu 版本
lsb_release -a

# 输出示例：
# Distributor ID: Ubuntu
# Description:    Ubuntu 22.04.3 LTS
# Release:        22.04
# Codename:       jammy
```

**支持的版本**：
- Ubuntu 20.04 LTS (Focal Fossa)
- Ubuntu 22.04 LTS (Jammy Jellyfish) ⭐ 推荐
- Ubuntu 24.04 LTS (Noble Numbat)

### 更新系统

```bash
# 更新软件包列表
sudo apt update

# 升级已安装的软件包（可选，但推荐）
sudo apt upgrade -y

# 如果提示重启，执行：
sudo reboot
```

### 安装基础工具

```bash
# 安装必要的系统工具
sudo apt install -y \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    curl \
    wget \
    git \
    vim \
    nano \
    build-essential \
    libssl-dev \
    libffi-dev \
    python3-dev

# 验证安装
git --version
curl --version
```

---

## Python 环境安装

### 方法 1: 安装 Python 3.11（推荐）

#### 步骤 1: 添加 deadsnakes PPA

```bash
# 添加 PPA 仓库（提供最新 Python 版本）
sudo add-apt-repository ppa:deadsnakes/ppa -y

# 更新软件包列表
sudo apt update
```

#### 步骤 2: 安装 Python 3.11

```bash
# 安装 Python 3.11 及相关组件
sudo apt install -y \
    python3.11 \
    python3.11-venv \
    python3.11-dev \
    python3.11-distutils

# 验证安装
python3.11 --version
# 输出：Python 3.11.x
```

#### 步骤 3: 安装 pip

```bash
# 下载 get-pip.py
curl -sS https://bootstrap.pypa.io/get-pip.py -o get-pip.py

# 使用 Python 3.11 安装 pip
python3.11 get-pip.py

# 验证 pip 安装
python3.11 -m pip --version

# 清理安装文件
rm get-pip.py
```

#### 步骤 4: 设置 Python 3.11 为默认版本（可选）

```bash
# 查看当前 Python 替代版本
sudo update-alternatives --list python3

# 设置 Python 3.11 为默认
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 1

# 设置优先级更高（如果有多个版本）
sudo update-alternatives --config python3
# 选择 python3.11

# 验证
python3 --version
# 输出：Python 3.11.x
```

### 方法 2: 使用系统自带的 Python（Ubuntu 22.04+）

```bash
# Ubuntu 22.04 默认自带 Python 3.10，也符合要求
python3 --version

# 安装 pip 和 venv
sudo apt install -y python3-pip python3-venv

# 验证
pip3 --version
```

### 方法 3: 使用 pyenv（高级用户）

```bash
# 安装依赖
sudo apt install -y make build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
    libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
    libffi-dev liblzma-dev

# 安装 pyenv
curl https://pyenv.run | bash

# 添加到 shell 配置
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc

# 重新加载配置
source ~/.bashrc

# 安装 Python 3.11
pyenv install 3.11.0

# 设置全局版本
pyenv global 3.11.0

# 验证
python --version
```

---

## 项目安装

### 步骤 1: 选择安装目录

```bash
# 推荐安装在用户主目录
cd ~

# 或者创建专门的项目目录
mkdir -p ~/projects
cd ~/projects
```

### 步骤 2: 克隆项目

```bash
# 克隆 GitHub 仓库
git clone https://github.com/HKUDS/AI-Trader.git

# 等待下载完成...
# Cloning into 'AI-Trader'...
# remote: Enumerating objects: xxx, done.
# ...

# 进入项目目录
cd AI-Trader

# 查看项目结构
ls -la
```

### 步骤 3: 创建虚拟环境

```bash
# 使用 Python 3.11 创建虚拟环境
python3.11 -m venv venv

# 或者使用系统默认 Python
python3 -m venv venv

# 验证虚拟环境已创建
ls -la venv/
# 应该看到 bin/, lib/, include/ 等目录
```

### 步骤 4: 激活虚拟环境

```bash
# 激活虚拟环境
source venv/bin/activate

# 提示符会变化，前面会显示 (venv)
# (venv) user@ubuntu:~/AI-Trader$

# 验证使用的是虚拟环境的 Python
which python
# 输出：/home/用户名/AI-Trader/venv/bin/python

python --version
# 输出：Python 3.11.x 或 3.10.x
```

**重要提示**：
- 每次打开新终端使用项目时，都需要重新激活虚拟环境
- 可以添加到 shell 配置文件自动激活：
```bash
# 添加别名到 ~/.bashrc
echo 'alias aitrader="cd ~/AI-Trader && source venv/bin/activate"' >> ~/.bashrc
source ~/.bashrc

# 以后只需要运行
aitrader
```

### 步骤 5: 升级 pip

```bash
# 确保虚拟环境已激活
# 升级 pip 到最新版本
pip install --upgrade pip

# 验证 pip 版本
pip --version
# 输出：pip 24.x.x from /home/用户名/AI-Trader/venv/lib/python3.11/site-packages/pip (python 3.11)
```

### 步骤 6: 安装项目依赖

```bash
# 方法 1: 直接安装（推荐）
pip install -r requirements.txt

# 如果安装速度慢，可以使用国内镜像
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# 方法 2: 逐个安装（如果上面失败）
pip install langchain==1.0.2
pip install langchain-openai==1.0.1
pip install langchain-mcp-adapters>=0.1.0
pip install fastmcp==2.12.5
pip install tushare

# 验证安装
pip list
```

**安装成功的输出示例**：
```
Package                Version
---------------------- -------
fastmcp                2.12.5
langchain              1.0.2
langchain-core         0.x.x
langchain-mcp-adapters 0.1.0
langchain-openai       1.0.1
tushare                1.x.x
...
```

### 步骤 7: 验证安装

```bash
# 测试导入核心包
python -c "import langchain; print(f'LangChain version: {langchain.__version__}')"
python -c "import fastmcp; print('FastMCP imported successfully')"
python -c "import tushare; print('Tushare imported successfully')"

# 如果没有错误输出，说明安装成功
```

---

## API 密钥获取

### 1. OpenAI API Key

#### 方法 1: 官方 OpenAI（国际版）

1. 访问 [https://platform.openai.com/](https://platform.openai.com/)
2. 点击右上角 "Sign up" 注册账户（或 "Log in" 登录）
3. 完成邮箱验证
4. 点击左侧菜单 "API keys"
5. 点击 "Create new secret key"
6. 输入名称（如 "AI-Trader"），点击创建
7. **立即复制并保存密钥**（只显示一次）

**注意事项**：
- 需要国际信用卡绑定
- 可能需要科学上网
- 有免费额度，超出需付费

#### 方法 2: 国内 OpenAI API 代理商

可以使用国内的 API 代理服务（支持支付宝/微信支付）：
- OpenAI-SB
- API2D
- CloseAI
- 等其他可靠的代理服务

配置示例：
```bash
OPENAI_API_BASE="https://your-proxy.com/v1"  # 代理商提供的地址
OPENAI_API_KEY="sk-xxxxx"  # 代理商提供的密钥
```

#### 方法 3: 使用 Claude API（推荐）

```bash
# 如果使用 Anthropic Claude
OPENAI_API_BASE="https://api.anthropic.com/v1"
OPENAI_API_KEY="sk-ant-xxxxx"  # Anthropic API Key
```

### 2. Alpha Vantage API Key（美股数据）

#### 免费版获取步骤：

1. 访问 [https://www.alphavantage.co/support/#api-key](https://www.alphavantage.co/support/#api-key)
2. 填写表单：
   - First Name: 名字
   - Last Name: 姓氏
   - Email: 邮箱地址
   - Organization: 填写 "Personal" 或 "Research"
3. 勾选 "I'm not a robot"
4. 点击 "GET FREE API KEY"
5. API Key 会立即显示并发送到邮箱

**示例 API Key**：
```
ALPHAADVANTAGE_API_KEY=ABC123XYZ456
```

**限制**：
- 免费版：每分钟 5 次请求，每天 100 次请求
- 足够日常使用和测试

### 3. Jina AI API Key（信息搜索）

1. 访问 [https://jina.ai/](https://jina.ai/)
2. 点击右上角 "Sign In" 或 "Get Started"
3. 使用 GitHub/Google 账号登录
4. 进入 Dashboard
5. 找到 API Keys 部分
6. 点击 "Create New API Key"
7. 复制密钥

**示例 API Key**：
```
JINA_API_KEY=jina_xxxxxxxxxxxxx
```

### 4. Tushare Token（A 股数据，可选）

#### 如果需要交易 A 股：

1. 访问 [https://tushare.pro/register](https://tushare.pro/register)
2. 注册账户（支持手机号注册）
3. 登录后进入 [个人中心](https://tushare.pro/user/token)
4. 复制 Token

**示例 Token**：
```
TUSHARE_TOKEN=1234567890abcdef1234567890abcdef
```

**注意**：
- 新用户有积分限制
- 可能需要参与社区活动获取积分
- 或者充值获取更高权限

**获取积分方法**：
- 完善个人资料：+100 积分
- 每日签到：+1 积分
- 分享文章：+10 积分
- 充值：100 元 = 2000 积分

---

## 环境配置

### 步骤 1: 创建 .env 文件

```bash
# 确保在项目根目录
cd ~/AI-Trader

# 复制示例配置文件
cp .env.example .env

# 查看文件
cat .env
```

### 步骤 2: 编辑 .env 文件

#### 使用 nano 编辑器（推荐新手）

```bash
nano .env
```

**编辑步骤**：
1. 使用方向键移动光标
2. 找到需要填写的字段
3. 删除原有内容，填入你的 API Key
4. 按 `Ctrl + O` 保存
5. 按 `Enter` 确认文件名
6. 按 `Ctrl + X` 退出

#### 使用 vim 编辑器（高级用户）

```bash
vim .env
```

**编辑步骤**：
1. 按 `i` 进入插入模式
2. 编辑内容
3. 按 `Esc` 退出插入模式
4. 输入 `:wq` 保存并退出

#### 使用 VS Code（如果已安装）

```bash
code .env
```

### 步骤 3: 填写配置内容

**完整的 .env 文件示例**：

```bash
# ==============================================
# AI 模型 API 配置
# ==============================================

# OpenAI API 配置（必需）
# 官方地址: https://api.openai.com/v1
# 国内代理: 使用代理商提供的地址
OPENAI_API_BASE="https://api.openai.com/v1"
OPENAI_API_KEY="sk-proj-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# 或者使用 Anthropic Claude
# OPENAI_API_BASE="https://api.anthropic.com/v1"
# OPENAI_API_KEY="sk-ant-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# ==============================================
# 数据源 API 配置
# ==============================================

# Alpha Vantage API（美股数据，必需）
# 获取地址: https://www.alphavantage.co/support/#api-key
ALPHAADVANTAGE_API_KEY="ABC123XYZ456DEMO"

# Jina AI API（信息搜索，必需）
# 获取地址: https://jina.ai/
JINA_API_KEY="jina_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Tushare Token（A 股数据，交易 A 股时必需）
# 获取地址: https://tushare.pro/user/token
TUSHARE_TOKEN="1234567890abcdef1234567890abcdef"

# ==============================================
# 服务端口配置
# ==============================================

# MCP 服务端口（默认即可，如有冲突请修改）
MATH_HTTP_PORT=8000
SEARCH_HTTP_PORT=8001
TRADE_HTTP_PORT=8002
GETPRICE_HTTP_PORT=8003

# ==============================================
# AI 代理配置
# ==============================================

# 最大推理步数
AGENT_MAX_STEP=30

# 运行环境配置文件路径（使用绝对路径）
# 替换 /home/你的用户名 为实际路径
RUNTIME_ENV_PATH="/home/你的用户名/AI-Trader/runtime_env.json"
```

### 步骤 4: 获取当前用户路径

```bash
# 获取完整的项目路径
pwd
# 输出示例：/home/ubuntu/AI-Trader

# 自动设置 RUNTIME_ENV_PATH
CURRENT_PATH=$(pwd)
sed -i "s|RUNTIME_ENV_PATH = \"\"|RUNTIME_ENV_PATH=\"${CURRENT_PATH}/runtime_env.json\"|g" .env

# 验证修改
grep RUNTIME_ENV_PATH .env
```

### 步骤 5: 验证配置

```bash
# 检查 .env 文件内容
cat .env

# 验证 API Key 是否已填写（不显示完整密钥）
grep -E "OPENAI_API_KEY|ALPHAADVANTAGE_API_KEY|JINA_API_KEY" .env

# 测试加载环境变量
python3 << EOF
import os
from dotenv import load_dotenv

load_dotenv()

print("OpenAI API Base:", os.getenv("OPENAI_API_BASE"))
print("OpenAI API Key:", os.getenv("OPENAI_API_KEY")[:10] + "..." if os.getenv("OPENAI_API_KEY") else "Not set")
print("Alpha Vantage Key:", os.getenv("ALPHAADVANTAGE_API_KEY")[:10] + "..." if os.getenv("ALPHAADVANTAGE_API_KEY") else "Not set")
print("Jina API Key:", os.getenv("JINA_API_KEY")[:10] + "..." if os.getenv("JINA_API_KEY") else "Not set")
EOF
```

### 步骤 6: 安装 python-dotenv（如果需要）

```bash
# 如果上面的测试失败，安装 python-dotenv
pip install python-dotenv

# 重新测试
```

---

## 数据准备

### 选项 A: 交易美股（纳斯达克 100）

#### 手动方式：

```bash
# 确保虚拟环境已激活
source venv/bin/activate

# 进入数据目录
cd data

# 查看数据获取脚本
cat get_daily_price.py

# 运行数据获取脚本
python get_daily_price.py
```

**预期输出**：
```
Fetching data for AAPL...
Successfully fetched AAPL data
Fetching data for MSFT...
Successfully fetched MSFT data
...
Progress: 50/100 stocks completed
...
All data fetched successfully!
```

**注意**：
- 由于 Alpha Vantage 免费版限制（每分钟 5 次请求）
- 脚本会自动添加延迟（每个股票约 15 秒）
- 完整下载 100 只股票需要约 25-30 分钟
- **请耐心等待，不要中断！**

#### 如果下载中断：

```bash
# 脚本会记录已下载的股票
# 可以重新运行，会跳过已下载的数据
python get_daily_price.py
```

#### 合并数据：

```bash
# 确保仍在 data 目录
pwd
# 输出：/home/xxx/AI-Trader/data

# 运行数据合并脚本
python merge_jsonl.py

# 验证生成的文件
ls -lh merged.jsonl
# 输出示例：-rw-rw-r-- 1 user user 50M Nov 8 12:00 merged.jsonl
```

#### 返回项目根目录：

```bash
cd ..
pwd
# 输出：/home/xxx/AI-Trader
```

#### 使用脚本方式（推荐）：

```bash
# 确保在项目根目录
cd ~/AI-Trader
source venv/bin/activate

# 运行数据准备脚本
bash scripts/main_step1.sh
```

**脚本会自动**：
1. 检查 Python 环境
2. 检查 API Key 配置
3. 下载股票数据
4. 合并为 JSONL 格式
5. 验证数据完整性

### 选项 B: 交易 A 股（上证 50）

#### 前提条件：

```bash
# 必须配置 Tushare Token
grep TUSHARE_TOKEN .env
# 输出：TUSHARE_TOKEN="你的token"
```

#### 手动方式：

```bash
# 激活虚拟环境
source venv/bin/activate

# 进入 A 股数据目录
cd data/A_stock

# 查看 A 股数据获取脚本
cat get_daily_price_a_stock.py

# 运行 A 股数据获取脚本
python get_daily_price_a_stock.py
```

**预期输出**：
```
Connecting to Tushare...
Successfully authenticated
Fetching SSE 50 constituent stocks...
Found 50 stocks in SSE 50 index
Fetching data for 600000.SH (浦发银行)...
Fetching data for 600016.SH (民生银行)...
...
All A-stock data fetched successfully!
Files created:
  - sse_50_weight.csv
  - daily_prices_sse_50.csv
  - index_daily_sse_50.json
```

#### 转换数据格式：

```bash
# 确保仍在 data/A_stock 目录
pwd
# 输出：/home/xxx/AI-Trader/data/A_stock

# 运行数据转换脚本
python merge_a_stock_jsonl.py

# 验证生成的文件
ls -lh merged.jsonl
# 输出示例：-rw-rw-r-- 1 user user 30M Nov 8 12:00 merged.jsonl

# 返回项目根目录
cd ../..
```

#### 使用脚本方式（推荐）：

```bash
# 确保在项目根目录
cd ~/AI-Trader
source venv/bin/activate

# 运行 A 股数据准备脚本
bash scripts/main_a_stock_step1.sh
```

### 验证数据准备成功

```bash
# 检查美股数据
if [ -f "data/merged.jsonl" ]; then
    echo "✅ 美股数据准备完成"
    wc -l data/merged.jsonl
else
    echo "❌ 美股数据不存在"
fi

# 检查 A 股数据
if [ -f "data/A_stock/merged.jsonl" ]; then
    echo "✅ A 股数据准备完成"
    wc -l data/A_stock/merged.jsonl
else
    echo "❌ A 股数据不存在"
fi

# 查看数据样本（美股）
head -n 1 data/merged.jsonl | python -m json.tool

# 查看数据样本（A 股）
head -n 1 data/A_stock/merged.jsonl | python -m json.tool
```

---

## 服务启动

### 步骤 1: 检查端口可用性

```bash
# 检查端口是否被占用
sudo netstat -tulpn | grep -E '8000|8001|8002|8003'

# 如果没有输出，说明端口可用
# 如果有输出，说明端口被占用，需要修改 .env 中的端口配置
```

### 步骤 2: 启动 MCP 服务

#### 方法 1: 使用脚本启动（推荐）

```bash
# 确保在项目根目录且虚拟环境已激活
cd ~/AI-Trader
source venv/bin/activate

# 启动 MCP 服务
bash scripts/main_step2.sh
```

**预期输出**：
```
Starting MCP services...
[INFO] Math service starting on port 8000...
[INFO] Search service starting on port 8001...
[INFO] Trade service starting on port 8002...
[INFO] Price service starting on port 8003...
All MCP services started successfully!
```

#### 方法 2: 手动启动

```bash
# 激活虚拟环境
source venv/bin/activate

# 进入工具目录
cd agent_tools

# 后台启动 MCP 服务
nohup python start_mcp_services.py > mcp_services.log 2>&1 &

# 记录进程 ID
echo $! > mcp_services.pid

# 查看日志
tail -f mcp_services.log

# 按 Ctrl+C 退出日志查看（服务继续运行）

# 返回项目根目录
cd ..
```

### 步骤 3: 验证 MCP 服务

```bash
# 检查服务进程
ps aux | grep start_mcp_services

# 检查端口监听
sudo netstat -tulpn | grep -E '8000|8001|8002|8003'

# 输出示例：
# tcp  0  0 0.0.0.0:8000  0.0.0.0:*  LISTEN  12345/python
# tcp  0  0 0.0.0.0:8001  0.0.0.0:*  LISTEN  12345/python
# tcp  0  0 0.0.0.0:8002  0.0.0.0:*  LISTEN  12345/python
# tcp  0  0 0.0.0.0:8003  0.0.0.0:*  LISTEN  12345/python

# 测试服务响应（如果服务提供了健康检查端点）
curl -s http://localhost:8000/health || echo "Service might not have health endpoint"
```

### 步骤 4: 运行交易代理

#### 美股交易：

```bash
# 方法 1: 使用默认配置
python main.py

# 方法 2: 指定配置文件
python main.py configs/default_config.json

# 方法 3: 使用脚本
bash scripts/main_step3.sh
```

#### A 股交易：

```bash
# 方法 1: 指定 A 股配置
python main.py configs/astock_config.json

# 方法 2: 使用脚本
bash scripts/main_a_stock_step3.sh
```

#### 预期输出：

```
Starting AI Trading System...
Loading configuration: configs/default_config.json
Initializing models: ['claude-3.7-sonnet', 'gpt-4o']
Date range: 2024-01-01 to 2024-03-31

[2024-01-01 09:30:00] Trading session started
[2024-01-01 09:30:01] claude-3.7-sonnet: Analyzing market...
[2024-01-01 09:30:05] claude-3.7-sonnet: Decided to buy AAPL (10 shares)
[2024-01-01 09:30:06] Trade executed: BUY AAPL x 10 @ $255.88
...
```

### 步骤 5: 监控运行状态

#### 打开新终端监控日志：

```bash
# 终端 1: 监控 MCP 服务日志
cd ~/AI-Trader/agent_tools
tail -f mcp_services.log

# 终端 2: 监控交易日志（如果配置了）
cd ~/AI-Trader/data/agent_data
tail -f */log/*/log.jsonl

# 终端 3: 监控系统资源
htop
# 或
top
```

---

## 验证测试

### 测试 1: 快速功能测试

创建一个测试配置文件：

```bash
# 创建测试配置
cat > configs/test_config.json << 'EOF'
{
  "agent_type": "BaseAgent",
  "market": "us",
  "date_range": {
    "init_date": "2024-01-01",
    "end_date": "2024-01-05"
  },
  "models": [
    {
      "name": "test-model",
      "basemodel": "gpt-3.5-turbo",
      "signature": "test-run",
      "enabled": true
    }
  ],
  "agent_config": {
    "max_steps": 10,
    "initial_cash": 10000.0
  }
}
EOF

# 运行测试
python main.py configs/test_config.json
```

### 测试 2: 验证数据访问

```bash
# 测试美股数据读取
python << 'EOF'
import json

# 读取美股数据
with open('data/merged.jsonl', 'r') as f:
    first_line = f.readline()
    data = json.loads(first_line)
    print("✅ 美股数据格式正确")
    print(f"股票代码: {data['Meta Data']['2. Symbol']}")
    print(f"最后更新: {data['Meta Data']['3. Last Refreshed']}")
EOF
```

### 测试 3: 验证 API 连接

```bash
# 测试 OpenAI API
python << 'EOF'
import os
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()

client = OpenAI(
    api_key=os.getenv("OPENAI_API_KEY"),
    base_url=os.getenv("OPENAI_API_BASE")
)

try:
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": "Say 'API working'"}],
        max_tokens=10
    )
    print("✅ OpenAI API 连接成功")
    print(f"响应: {response.choices[0].message.content}")
except Exception as e:
    print(f"❌ OpenAI API 连接失败: {e}")
EOF
```

### 测试 4: 验证完整流程

```bash
# 创建一个完整的测试脚本
cat > test_installation.sh << 'EOF'
#!/bin/bash

echo "========================================"
echo "AI-Trader 安装验证脚本"
echo "========================================"

# 测试 1: Python 版本
echo -n "测试 Python 版本... "
PYTHON_VERSION=$(python --version 2>&1 | awk '{print $2}')
if [[ "$PYTHON_VERSION" > "3.10" ]]; then
    echo "✅ $PYTHON_VERSION"
else
    echo "❌ 版本过低: $PYTHON_VERSION"
    exit 1
fi

# 测试 2: 虚拟环境
echo -n "测试虚拟环境... "
if [[ "$VIRTUAL_ENV" != "" ]]; then
    echo "✅ 已激活"
else
    echo "❌ 未激活"
    exit 1
fi

# 测试 3: 依赖包
echo -n "测试依赖包... "
if python -c "import langchain, fastmcp, tushare" 2>/dev/null; then
    echo "✅ 已安装"
else
    echo "❌ 未安装"
    exit 1
fi

# 测试 4: 环境变量
echo -n "测试环境变量... "
if [ -f ".env" ]; then
    echo "✅ .env 文件存在"
else
    echo "❌ .env 文件不存在"
    exit 1
fi

# 测试 5: 数据文件
echo -n "测试美股数据... "
if [ -f "data/merged.jsonl" ]; then
    echo "✅ 数据已准备"
else
    echo "⚠️  数据未准备"
fi

# 测试 6: MCP 服务
echo -n "测试 MCP 服务... "
if netstat -tuln 2>/dev/null | grep -q ":8000\|:8001\|:8002\|:8003"; then
    echo "✅ 服务运行中"
else
    echo "⚠️  服务未启动"
fi

echo "========================================"
echo "验证完成！"
echo "========================================"
EOF

# 添加执行权限
chmod +x test_installation.sh

# 运行测试
./test_installation.sh
```

---

## 启动 Web 界面

### 方法 1: 使用脚本

```bash
# 使用提供的启动脚本
bash scripts/start_ui.sh
```

### 方法 2: 手动启动

```bash
# 进入文档目录
cd docs

# 启动简单的 HTTP 服务器
python3 -m http.server 8888

# 输出：
# Serving HTTP on 0.0.0.0 port 8888 (http://0.0.0.0:8888/) ...
```

### 访问 Web 界面

```bash
# 在服务器上打开浏览器
xdg-open http://localhost:8888

# 或使用 Firefox
firefox http://localhost:8888 &

# 或使用 Chrome
google-chrome http://localhost:8888 &
```

**如果是远程服务器**：

```bash
# 查看服务器 IP
ip addr show | grep inet

# 在本地浏览器访问
http://服务器IP:8888
```

**使用 SSH 隧道（更安全）**：

```bash
# 在本地电脑运行
ssh -L 8888:localhost:8888 用户名@服务器IP

# 然后在本地浏览器访问
http://localhost:8888
```

---

## 常见问题详解

### 问题 1: ModuleNotFoundError: No module named 'xxx'

**症状**：
```
ModuleNotFoundError: No module named 'langchain'
```

**原因**：
- 虚拟环境未激活
- 依赖未安装
- 使用了错误的 Python 解释器

**解决方案**：
```bash
# 1. 确保虚拟环境已激活
source venv/bin/activate
which python
# 应该输出：/home/xxx/AI-Trader/venv/bin/python

# 2. 重新安装依赖
pip install -r requirements.txt

# 3. 验证安装
pip list | grep langchain
```

### 问题 2: Permission denied

**症状**：
```
Permission denied: '/home/user/AI-Trader/data/agent_data'
```

**原因**：
- 文件或目录权限不足

**解决方案**：
```bash
# 修改项目目录权限
chmod -R u+rwx ~/AI-Trader

# 或者改变所有者
sudo chown -R $USER:$USER ~/AI-Trader

# 验证权限
ls -la ~/AI-Trader
```

### 问题 3: Port already in use

**症状**：
```
Error: Address already in use (port 8000)
```

**原因**：
- 端口被其他程序占用

**解决方案**：

```bash
# 方法 1: 查找并终止占用端口的进程
sudo lsof -i :8000
# 输出示例：
# COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
# python  12345 user    3u  IPv4  xxxxx      0t0  TCP *:8000 (LISTEN)

# 终止进程
sudo kill -9 12345

# 方法 2: 修改 .env 文件使用其他端口
nano .env
# 将 MATH_HTTP_PORT=8000 改为 MATH_HTTP_PORT=8100

# 方法 3: 使用脚本自动查找可用端口
python << 'EOF'
import socket

def find_free_port(start=8000):
    for port in range(start, start + 100):
        try:
            s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            s.bind(('', port))
            s.close()
            return port
        except OSError:
            continue
    return None

print(f"可用端口: {find_free_port()}")
EOF
```

### 问题 4: SSL Certificate Error

**症状**：
```
SSL: CERTIFICATE_VERIFY_FAILED
```

**原因**：
- 系统缺少 CA 证书
- 网络代理问题

**解决方案**：

```bash
# 方法 1: 更新 CA 证书
sudo apt update
sudo apt install ca-certificates -y
sudo update-ca-certificates

# 方法 2: 安装 certifi
pip install --upgrade certifi

# 方法 3: 临时禁用 SSL 验证（不推荐用于生产）
export CURL_CA_BUNDLE=""
export REQUESTS_CA_BUNDLE=""
```

### 问题 5: Alpha Vantage API Rate Limit

**症状**：
```
API rate limit exceeded. Please try again later.
```

**原因**：
- 超过每分钟 5 次请求限制

**解决方案**：

```bash
# 方法 1: 等待 1 分钟后重试
sleep 60
python data/get_daily_price.py

# 方法 2: 脚本已内置延迟，耐心等待完成
# 不要中断脚本

# 方法 3: 如果经常遇到，考虑升级 API 计划
# 访问: https://www.alphavantage.co/premium/
```

### 问题 6: Tushare Token Invalid

**症状**：
```
Tushare API Error: Token invalid
```

**解决方案**：

```bash
# 1. 检查 Token 是否正确
grep TUSHARE_TOKEN .env

# 2. 验证 Token
python << 'EOF'
import os
import tushare as ts
from dotenv import load_dotenv

load_dotenv()
token = os.getenv('TUSHARE_TOKEN')

try:
    ts.set_token(token)
    pro = ts.pro_api()
    df = pro.stock_basic(exchange='', list_status='L', fields='ts_code,symbol,name')
    print(f"✅ Token 有效，获取到 {len(df)} 条记录")
except Exception as e:
    print(f"❌ Token 无效: {e}")
EOF

# 3. 如果无效，重新获取 Token
# 访问: https://tushare.pro/user/token
```

### 问题 7: 内存不足 (Out of Memory)

**症状**：
```
MemoryError: Unable to allocate array
```

**解决方案**：

```bash
# 1. 检查可用内存
free -h

# 2. 创建交换空间（临时）
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 验证
free -h

# 3. 永久添加（编辑 /etc/fstab）
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# 4. 减少并发模型数量
# 编辑配置文件，只启用 1-2 个模型

# 5. 释放内存
sync
echo 3 | sudo tee /proc/sys/vm/drop_caches
```

### 问题 8: 虚拟环境无法创建

**症状**：
```
Error: Command 'python3 -m venv venv' failed
```

**解决方案**：

```bash
# 1. 安装 venv 模块
sudo apt install python3.11-venv -y

# 2. 或使用 virtualenv
pip install virtualenv
virtualenv venv

# 3. 验证
source venv/bin/activate
```

### 问题 9: Git Clone 失败

**症状**：
```
fatal: unable to access 'https://github.com/...': Could not resolve host
```

**解决方案**：

```bash
# 1. 检查网络连接
ping github.com

# 2. 检查 DNS
nslookup github.com

# 3. 修改 DNS（如果需要）
sudo nano /etc/resolv.conf
# 添加：
# nameserver 8.8.8.8
# nameserver 1.1.1.1

# 4. 使用 SSH 方式克隆（如果配置了 SSH 密钥）
git clone git@github.com:HKUDS/AI-Trader.git

# 5. 或下载 ZIP 文件
wget https://github.com/HKUDS/AI-Trader/archive/refs/heads/main.zip
unzip main.zip
mv AI-Trader-main AI-Trader
```

### 问题 10: 数据文件损坏

**症状**：
```
JSONDecodeError: Expecting value
```

**解决方案**：

```bash
# 1. 验证 JSON 格式
python -m json.tool data/merged.jsonl

# 2. 如果损坏，重新生成
cd data
rm merged.jsonl
python merge_jsonl.py

# 3. 如果源数据也损坏，重新下载
rm daily_prices_*.json
python get_daily_price.py
python merge_jsonl.py
```

---

## 完整示例流程

### 从零开始的完整安装（复制粘贴版）

```bash
# ==================== 第 1 部分：系统准备 ====================

# 更新系统
sudo apt update && sudo apt upgrade -y

# 安装 Python 3.11
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install -y python3.11 python3.11-venv python3.11-dev python3-pip

# 安装系统依赖
sudo apt install -y git curl wget build-essential libssl-dev libffi-dev

# 验证 Python
python3.11 --version

# ==================== 第 2 部分：项目安装 ====================

# 进入主目录
cd ~

# 克隆项目
git clone https://github.com/HKUDS/AI-Trader.git
cd AI-Trader

# 创建虚拟环境
python3.11 -m venv venv

# 激活虚拟环境
source venv/bin/activate

# 升级 pip
pip install --upgrade pip

# 安装依赖
pip install -r requirements.txt

# ==================== 第 3 部分：环境配置 ====================

# 复制环境变量文件
cp .env.example .env

# 编辑 .env 文件（填入你的 API Keys）
nano .env

# 自动设置路径
CURRENT_PATH=$(pwd)
sed -i "s|RUNTIME_ENV_PATH = \"\"|RUNTIME_ENV_PATH=\"${CURRENT_PATH}/runtime_env.json\"|g" .env

# ==================== 第 4 部分：数据准备 ====================

# 美股数据
bash scripts/main_step1.sh

# 或 A 股数据（需要 Tushare Token）
bash scripts/main_a_stock_step1.sh

# ==================== 第 5 部分：启动服务 ====================

# 启动 MCP 服务
bash scripts/main_step2.sh

# 运行交易（美股）
bash scripts/main_step3.sh

# 或运行交易（A 股）
bash scripts/main_a_stock_step3.sh

# ==================== 第 6 部分：启动 Web 界面 ====================

# 新开一个终端
bash scripts/start_ui.sh

# 浏览器访问: http://localhost:8888
```

### 快速测试版（5 分钟验证）

```bash
# 1. 安装基础环境
sudo apt update
sudo apt install -y python3-pip python3-venv git

# 2. 克隆项目
cd ~
git clone https://github.com/HKUDS/AI-Trader.git
cd AI-Trader

# 3. 设置虚拟环境
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# 4. 配置环境（最小配置）
cp .env.example .env
# 手动编辑 .env 添加 OPENAI_API_KEY

# 5. 运行测试
python -c "import langchain; print('✅ 安装成功')"
```

---

## 性能调优

### 1. 优化数据加载速度

```bash
# 使用 SSD 存储（如果可能）
# 将项目移至 SSD 分区
sudo mv ~/AI-Trader /path/to/ssd/AI-Trader
ln -s /path/to/ssd/AI-Trader ~/AI-Trader
```

### 2. 调整系统限制

```bash
# 增加文件描述符限制
echo "* soft nofile 65536" | sudo tee -a /etc/security/limits.conf
echo "* hard nofile 65536" | sudo tee -a /etc/security/limits.conf

# 重新登录生效
ulimit -n 65536
```

### 3. 使用多核处理

```bash
# 修改配置支持并行
# 编辑 main_parrallel.py 或配置文件
```

### 4. 监控系统资源

```bash
# 安装监控工具
sudo apt install htop iotop nethogs -y

# 实时监控
htop

# 监控磁盘 I/O
sudo iotop

# 监控网络
sudo nethogs
```

---

## 自动化脚本

### 创建一键安装脚本

```bash
cat > ~/install_ai_trader.sh << 'SCRIPT'
#!/bin/bash

set -e

echo "=========================================="
echo "AI-Trader 自动安装脚本"
echo "=========================================="

# 检查是否为 Ubuntu
if ! grep -q "Ubuntu" /etc/os-release; then
    echo "❌ 此脚本仅支持 Ubuntu"
    exit 1
fi

# 更新系统
echo "📦 更新系统..."
sudo apt update

# 安装 Python 3.11
echo "🐍 安装 Python 3.11..."
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install -y python3.11 python3.11-venv python3.11-dev python3-pip git curl

# 克隆项目
echo "📥 克隆项目..."
cd ~
if [ -d "AI-Trader" ]; then
    echo "⚠️  AI-Trader 目录已存在，跳过克隆"
    cd AI-Trader
else
    git clone https://github.com/HKUDS/AI-Trader.git
    cd AI-Trader
fi

# 创建虚拟环境
echo "🔧 创建虚拟环境..."
python3.11 -m venv venv
source venv/bin/activate

# 安装依赖
echo "📦 安装依赖..."
pip install --upgrade pip
pip install -r requirements.txt

# 配置环境
echo "⚙️  配置环境..."
if [ ! -f ".env" ]; then
    cp .env.example .env
    CURRENT_PATH=$(pwd)
    sed -i "s|RUNTIME_ENV_PATH = \"\"|RUNTIME_ENV_PATH=\"${CURRENT_PATH}/runtime_env.json\"|g" .env
    echo "⚠️  请编辑 .env 文件填入 API Keys"
fi

# 创建启动别名
echo "🔗 创建快捷命令..."
if ! grep -q "alias aitrader" ~/.bashrc; then
    echo "alias aitrader='cd ~/AI-Trader && source venv/bin/activate'" >> ~/.bashrc
fi

echo "=========================================="
echo "✅ 安装完成！"
echo "=========================================="
echo ""
echo "下一步："
echo "1. 编辑 .env 文件: nano ~/AI-Trader/.env"
echo "2. 获取 API Keys 并填入 .env"
echo "3. 准备数据: bash ~/AI-Trader/scripts/main_step1.sh"
echo "4. 启动服务: bash ~/AI-Trader/scripts/main_step2.sh"
echo "5. 运行交易: bash ~/AI-Trader/scripts/main_step3.sh"
echo ""
echo "快捷命令: 执行 'aitrader' 即可进入项目并激活环境"
SCRIPT

# 添加执行权限
chmod +x ~/install_ai_trader.sh

# 运行安装
bash ~/install_ai_trader.sh
```

---

## 下一步建议

安装完成后，建议：

1. **阅读文档**
   - [README_CN.md](README_CN.md) - 完整功能介绍
   - [Communication.md](Communication.md) - 社区交流

2. **自定义配置**
   - 修改 `configs/default_config.json`
   - 调整交易日期范围
   - 选择不同的 AI 模型

3. **运行回测**
   - 选择历史时间段
   - 对比不同模型表现
   - 分析交易决策

4. **加入社区**
   - GitHub Discussions
   - 微信/飞书群

---

## 附录

### 常用命令速查

```bash
# 激活环境
cd ~/AI-Trader && source venv/bin/activate

# 更新项目
git pull origin main

# 更新依赖
pip install --upgrade -r requirements.txt

# 查看日志
tail -f agent_tools/mcp_services.log

# 停止服务
pkill -f start_mcp_services

# 清理数据
rm -rf data/agent_data/*

# 重新准备数据
bash scripts/main_step1.sh
```

### 系统要求检查脚本

```bash
cat > check_requirements.sh << 'EOF'
#!/bin/bash

echo "系统要求检查"
echo "============="

# Python 版本
PYTHON_VERSION=$(python3 --version 2>&1 | grep -oP '\d+\.\d+' | head -1)
echo -n "Python 版本: $PYTHON_VERSION "
if (( $(echo "$PYTHON_VERSION >= 3.10" | bc -l) )); then
    echo "✅"
else
    echo "❌ (需要 >= 3.10)"
fi

# 内存
TOTAL_MEM=$(free -g | awk '/^Mem:/{print $2}')
echo -n "内存: ${TOTAL_MEM}GB "
if [ $TOTAL_MEM -ge 4 ]; then
    echo "✅"
else
    echo "⚠️  (推荐 >= 4GB)"
fi

# 磁盘空间
AVAILABLE_SPACE=$(df -BG . | awk 'NR==2 {print $4}' | sed 's/G//')
echo -n "可用空间: ${AVAILABLE_SPACE}GB "
if [ $AVAILABLE_SPACE -ge 5 ]; then
    echo "✅"
else
    echo "❌ (需要 >= 5GB)"
fi

# Git
if command -v git &> /dev/null; then
    echo "Git: ✅"
else
    echo "Git: ❌"
fi
EOF

chmod +x check_requirements.sh
bash check_requirements.sh
```

---

**安装指南结束**

如有任何问题，请查看 [GitHub Issues](https://github.com/HKUDS/AI-Trader/issues) 或参与社区讨论。

祝您使用愉快！ 🚀
