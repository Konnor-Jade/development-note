# MCP (Model Context Protocol) 配置指南

> GitHub Copilot CLI 的 MCP 服务器配置和迁移指南

## 📋 简介

**MCP (Model Context Protocol)** 是一个开放协议，用于标准化应用程序向大型语言模型（LLM）提供上下文的方式。它就像是 AI 应用的 "USB-C 端口"，使 AI 模型能够连接到各种数据源和工具。

**官方文档：** https://modelcontextprotocol.io  
**规范文档：** https://spec.modelcontextprotocol.io

### 核心概念

MCP 允许 LLM 通过标准化接口访问：
- **Resources（资源）** - 上下文数据和信息
- **Tools（工具）** - 可执行的功能和操作
- **Prompts（提示）** - 预定义的提示模板

---

## 🚀 配置文件位置

MCP 配置文件位于：
```
~/.copilot/mcp-config.json
```

其他相关配置：
```
~/.copilot/config.json           # Copilot 主配置
~/.copilot/logs/                 # 日志目录
~/.copilot/session-state/        # 会话状态
```

---

## 📝 配置文件结构

### 基础配置格式

```json
{
  "mcpServers": {
    "server-name": {
      "type": "local|http",
      "command": "command-to-run",
      "args": ["arg1", "arg2"],
      "tools": ["*"],
      // HTTP 类型特有
      "url": "https://...",
      "headers": {
        "API_KEY": "your-key"
      }
    }
  }
}
```

### 配置类型

#### 1. Local 类型（本地服务）

适用于：通过 npx 或本地命令启动的 MCP 服务器

```json
{
  "type": "local",
  "command": "npx",
  "args": ["-y", "package-name"],
  "tools": ["*"]
}
```

#### 2. HTTP 类型（远程服务）

适用于：HTTP API 形式的 MCP 服务器

```json
{
  "type": "http",
  "url": "https://api.example.com/mcp",
  "headers": {
    "API_KEY": "your-api-key"
  },
  "tools": ["tool1", "tool2"]
}
```

---

## 🔧 常用 MCP 服务器配置

### 1. Workflow3（工作流管理）

**功能：** 提供三阶段工作流提示管理

```json
{
  "workflow3": {
    "type": "local",
    "command": "npx",
    "args": [
      "-y",
      "--registry",
      "https://registry.npmmirror.com",
      "workflow3"
    ],
    "tools": ["*"]
  }
}
```

**使用场景：** 
- 项目规划和任务管理
- 多步骤工作流程执行

---

### 2. Context7（开发文档查询）

**功能：** 查询最新的库文档和代码示例

```json
{
  "context7": {
    "type": "http",
    "url": "https://mcp.context7.com/mcp",
    "headers": {
      "CONTEXT7_API_KEY": "ctx7sk-YOUR_API_KEY_HERE"
    },
    "tools": [
      "get-library-docs",
      "resolve-library-id"
    ]
  }
}
```

**获取 API Key：**
1. 访问 https://context7.com
2. 注册账户
3. 在设置中生成 API Key

**使用场景：**
- 查询最新的库文档
- 获取代码示例和 API 参考
- 支持 10000+ 开源项目

---

### 3. Chrome DevTools（浏览器自动化）

**功能：** 通过 Chrome DevTools 协议控制浏览器

```json
{
  "chrome-devtools": {
    "type": "local",
    "command": "npx",
    "args": [
      "-y",
      "chrome-devtools-mcp@latest"
    ],
    "tools": ["*"]
  }
}
```

**使用场景：**
- 网页自动化测试
- 爬取动态网页内容
- 浏览器交互自动化

---

### 4. Playwright（端到端测试）

**功能：** 跨浏览器自动化测试

```json
{
  "Playwright": {
    "type": "local",
    "command": "npx",
    "args": [
      "@playwright/mcp@latest"
    ],
    "tools": ["*"]
  }
}
```

**使用场景：**
- E2E 测试自动化
- 跨浏览器兼容性测试
- 截图和录制功能

---

### 5. 自定义本地服务器

**功能：** 运行自己开发的 MCP 服务器

```json
{
  "custom-server": {
    "type": "local",
    "command": "node",
    "args": [
      "/path/to/your/server/index.js"
    ],
    "tools": ["*"]
  }
}
```

---

## 📦 完整配置示例

### mcp-config.json（模板）

```json
{
  "mcpServers": {
    "workflow3": {
      "type": "local",
      "command": "npx",
      "args": [
        "-y",
        "--registry",
        "https://registry.npmmirror.com",
        "workflow3"
      ],
      "tools": ["*"]
    },
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "REPLACE_WITH_YOUR_KEY"
      },
      "tools": [
        "get-library-docs",
        "resolve-library-id"
      ]
    },
    "chrome-devtools": {
      "type": "local",
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest"
      ],
      "tools": ["*"]
    },
    "Playwright": {
      "type": "local",
      "command": "npx",
      "args": [
        "@playwright/mcp@latest"
      ],
      "tools": ["*"]
    }
  }
}
```

---

## 🔄 配置迁移指南

### 方法 1: 手动迁移（推荐）

#### 步骤 1: 备份配置（旧机器）

```bash
# 备份 MCP 配置
cp ~/.copilot/mcp-config.json ~/mcp-config-backup.json

# 如果需要，也备份主配置
cp ~/.copilot/config.json ~/copilot-config-backup.json
```

#### 步骤 2: 传输配置

```bash
# 方法 A: 使用云盘（推荐）
# 上传到 iCloud/Dropbox/OneDrive

# 方法 B: 使用 Git（如果配置已脱敏）
cd ~/dotfiles
cp ~/mcp-config-backup.json copilot/mcp-config.template.json
git add copilot/
git commit -m "Add MCP config template"
git push

# 方法 C: 通过网络传输
scp ~/mcp-config-backup.json user@new-machine:~/
```

#### 步骤 3: 恢复配置（新机器）

```bash
# 创建配置目录
mkdir -p ~/.copilot

# 复制配置文件
cp ~/mcp-config-backup.json ~/.copilot/mcp-config.json

# 设置正确的权限
chmod 600 ~/.copilot/mcp-config.json
```

#### 步骤 4: 更新敏感信息

```bash
# 打开配置文件
vim ~/.copilot/mcp-config.json

# 或使用你喜欢的编辑器
code ~/.copilot/mcp-config.json
nvim ~/.copilot/mcp-config.json
```

**需要更新的内容：**
- ✅ API Keys（如 Context7 API Key）
- ✅ 本地文件路径
- ✅ 用户特定配置

---

### 方法 2: 使用配置模板

#### 创建脱敏模板

```bash
# 创建模板目录
mkdir -p ~/dev/development-note/mcp

# 创建配置模板（已脱敏）
cat > ~/dev/development-note/mcp/mcp-config.template.json << 'EOF'
{
  "mcpServers": {
    "workflow3": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "--registry", "https://registry.npmmirror.com", "workflow3"],
      "tools": ["*"]
    },
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "REPLACE_WITH_YOUR_KEY"
      },
      "tools": ["get-library-docs", "resolve-library-id"]
    },
    "chrome-devtools": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"],
      "tools": ["*"]
    },
    "Playwright": {
      "type": "local",
      "command": "npx",
      "args": ["@playwright/mcp@latest"],
      "tools": ["*"]
    }
  }
}
EOF
```

#### 在新机器上使用

```bash
# 复制模板到配置目录
cp ~/dev/development-note/mcp/mcp-config.template.json ~/.copilot/mcp-config.json

# 编辑并填入实际的 API Keys
vim ~/.copilot/mcp-config.json

# 设置权限
chmod 600 ~/.copilot/mcp-config.json
```

---

### 方法 3: 使用环境变量（最安全）

#### 设置环境变量

```bash
# 在 ~/.zshrc 或 ~/.bashrc 中添加
cat >> ~/.zshrc << 'EOF'

# MCP 配置相关的环境变量
export CONTEXT7_API_KEY="your-actual-key-here"
export GITHUB_MCP_TOKEN="your-github-token"

EOF

# 重新加载配置
source ~/.zshrc
```

#### 配置文件引用环境变量

```json
{
  "mcpServers": {
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "${CONTEXT7_API_KEY}"
      },
      "tools": ["get-library-docs", "resolve-library-id"]
    }
  }
}
```

**注意：** 某些 MCP 实现可能不支持环境变量替换，需要直接填入值。

---

## 🔒 安全最佳实践

### 1. 保护 API Keys

```bash
# 永远不要提交真实的 API Keys 到 Git
echo "mcp-config.json" >> ~/.gitignore
echo ".env" >> ~/.gitignore

# 只提交模板文件
git add mcp-config.template.json
```

### 2. 设置正确的文件权限

```bash
# MCP 配置文件应该只有所有者可读写
chmod 600 ~/.copilot/mcp-config.json
chmod 600 ~/.copilot/config.json

# 目录权限
chmod 700 ~/.copilot
```

### 3. 使用 .env 文件

```bash
# 创建 .env 文件存储敏感信息
cat > ~/.copilot/.env << 'EOF'
CONTEXT7_API_KEY=your-actual-key
GITHUB_TOKEN=your-github-token
EOF

# 设置权限
chmod 600 ~/.copilot/.env

# 添加到 .gitignore
echo ".env" >> ~/.gitignore
```

### 4. 定期轮换 API Keys

- 定期更新 API Keys（建议每 3-6 个月）
- 发现泄露时立即撤销旧 Key
- 为不同机器使用不同的 Key（如果服务支持）

---

## 🛠 验证和测试

### 检查配置语法

```bash
# 使用 jq 验证 JSON 格式
jq . ~/.copilot/mcp-config.json

# 如果没有 jq，安装它
brew install jq  # macOS
```

### 查看配置

```bash
# 查看当前配置
cat ~/.copilot/mcp-config.json

# 查看日志
ls -lt ~/.copilot/logs/ | head -5

# 查看最新日志
tail -f ~/.copilot/logs/*.log
```

### 测试 MCP 服务器

```bash
# 重启 Copilot CLI 后测试
# 在 Copilot 对话中尝试使用配置的服务器

# 测试 Context7
# "use context7 to find React documentation"

# 测试 Workflow3
# "use workflow3 to plan a new project"
```

---

## 📋 常用 MCP 服务器列表

### 官方服务器

| 服务器 | 功能 | 安装命令 |
|--------|------|---------|
| **filesystem** | 文件系统访问 | `npx -y @modelcontextprotocol/server-filesystem` |
| **github** | GitHub API 集成 | `npx -y @modelcontextprotocol/server-github` |
| **postgres** | PostgreSQL 数据库 | `npx -y @modelcontextprotocol/server-postgres` |
| **puppeteer** | 浏览器自动化 | `npx -y @modelcontextprotocol/server-puppeteer` |
| **sqlite** | SQLite 数据库 | `npx -y @modelcontextprotocol/server-sqlite` |

### 第三方服务器

| 服务器 | 功能 | 链接 |
|--------|------|------|
| **AWS MCP** | AWS 服务集成 | https://github.com/awslabs/mcp |
| **Microsoft MCP** | Microsoft 服务 | https://github.com/microsoft/mcp |
| **Context7** | 文档查询 | https://context7.com |

---

## ❓ 常见问题

### Q1: MCP 服务器无法启动

**检查步骤：**
```bash
# 1. 检查 Node.js 版本
node --version  # 需要 v18+

# 2. 检查配置文件语法
jq . ~/.copilot/mcp-config.json

# 3. 查看日志
tail -20 ~/.copilot/logs/*.log

# 4. 测试 npx 命令
npx -y workflow3 --version
```

### Q2: API Key 错误

**解决方案：**
1. 检查 API Key 是否正确复制（无空格、换行）
2. 确认 API Key 是否过期
3. 检查 API Key 权限是否足够
4. 重新生成并更新配置

### Q3: 权限被拒绝

```bash
# 检查文件权限
ls -la ~/.copilot/

# 修复权限
chmod 700 ~/.copilot
chmod 600 ~/.copilot/mcp-config.json
chmod 600 ~/.copilot/config.json
```

### Q4: npx 网络问题（中国大陆）

**使用国内镜像：**
```json
{
  "args": [
    "-y",
    "--registry",
    "https://registry.npmmirror.com",
    "package-name"
  ]
}
```

### Q5: 配置更新不生效

```bash
# 重启 Copilot CLI
# 或清除缓存

rm -rf ~/.copilot/session-state/*
```

---

## 💡 高级技巧

### 1. 按环境切换配置

```bash
# 创建多个配置文件
~/.copilot/mcp-config.dev.json
~/.copilot/mcp-config.prod.json

# 创建切换脚本
cat > ~/switch-mcp.sh << 'EOF'
#!/bin/bash
ENV=${1:-dev}
cp ~/.copilot/mcp-config.$ENV.json ~/.copilot/mcp-config.json
echo "Switched to $ENV environment"
EOF

chmod +x ~/switch-mcp.sh
```

### 2. 配置迁移自动化脚本

```bash
cat > ~/migrate-mcp.sh << 'EOF'
#!/bin/bash

echo "🚀 MCP Configuration Migration Script"
echo "======================================"

# 备份现有配置
if [ -f ~/.copilot/mcp-config.json ]; then
    echo "📦 Backing up existing config..."
    cp ~/.copilot/mcp-config.json ~/.copilot/mcp-config.backup.json
fi

# 复制模板
echo "📝 Copying template..."
cp ~/dev/development-note/mcp/mcp-config.template.json ~/.copilot/mcp-config.json

# 设置权限
echo "🔒 Setting permissions..."
chmod 600 ~/.copilot/mcp-config.json

echo ""
echo "✅ Migration complete!"
echo "⚠️  Don't forget to update your API keys in:"
echo "    ~/.copilot/mcp-config.json"
EOF

chmod +x ~/migrate-mcp.sh
```

### 3. 配置验证脚本

```bash
cat > ~/validate-mcp.sh << 'EOF'
#!/bin/bash

echo "🔍 Validating MCP Configuration..."

# 检查文件是否存在
if [ ! -f ~/.copilot/mcp-config.json ]; then
    echo "❌ Config file not found!"
    exit 1
fi

# 检查 JSON 语法
if ! jq empty ~/.copilot/mcp-config.json 2>/dev/null; then
    echo "❌ Invalid JSON syntax!"
    exit 1
fi

# 检查权限
PERMS=$(stat -f "%A" ~/.copilot/mcp-config.json 2>/dev/null || stat -c "%a" ~/.copilot/mcp-config.json 2>/dev/null)
if [ "$PERMS" != "600" ]; then
    echo "⚠️  Warning: Incorrect permissions (should be 600)"
fi

# 检查是否有模板占位符
if grep -q "REPLACE_WITH_YOUR_KEY" ~/.copilot/mcp-config.json; then
    echo "⚠️  Warning: Found placeholder keys, please update them!"
fi

echo "✅ Configuration is valid!"
EOF

chmod +x ~/validate-mcp.sh
```

---

## 📚 相关资源

### 官方资源
- **MCP 官网**: https://modelcontextprotocol.io
- **MCP 规范**: https://spec.modelcontextprotocol.io
- **GitHub**: https://github.com/modelcontextprotocol

### SDK 和工具
- **TypeScript SDK**: https://github.com/modelcontextprotocol/typescript-sdk
- **Python SDK**: https://github.com/modelcontextprotocol/python-sdk
- **C# SDK**: https://modelcontextprotocol.github.io/csharp-sdk

### 社区和示例
- **MCP Servers**: https://github.com/modelcontextprotocol/servers
- **Microsoft MCP**: https://github.com/microsoft/mcp
- **AWS MCP**: https://github.com/awslabs/mcp

---

## 🎉 总结

MCP 配置的关键要点：
- 🔑 **保护敏感信息** - API Keys 永不提交到版本控制
- 📝 **使用模板** - 便于迁移和共享
- 🔒 **正确权限** - 600 for files, 700 for directories
- 📦 **模块化** - 按需启用服务器
- 🔄 **版本控制** - 使用 Git 管理模板
- ✅ **测试验证** - 迁移后验证配置

**配置好 MCP，让 AI 助手更强大！** 🚀✨
