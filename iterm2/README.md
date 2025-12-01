# iTerm2 配置指南

> macOS 终端模拟器 iTerm2 的配置备份与恢复指南

## 📋 简介

**iTerm2** 是 macOS 上功能强大的终端模拟器，提供了丰富的自定义选项和高级特性。

**官网：** https://iterm2.com  
**下载：** https://iterm2.com/downloads.html

---

## 📍 配置文件位置

iTerm2 的配置存储在：
```
~/Library/Preferences/com.googlecode.iterm2.plist
```

动态配置目录（如果启用）：
```
~/Library/Application Support/iTerm2/DynamicProfiles/
```

---

## 🔄 导出配置

### 方法 1: 使用 iTerm2 内置功能（推荐）

1. 打开 iTerm2
2. 菜单栏 → **Preferences** (或 `Cmd + ,`)
3. **General** → **Preferences**
4. 勾选 **Load preferences from a custom folder or URL**
5. 点击 **Browse** 选择保存位置（如 `~/dotfiles/iterm2`）
6. 点击 **Save Current Settings to Folder**

### 方法 2: 手动复制配置文件

```bash
# 创建备份目录
mkdir -p ~/dotfiles/iterm2

# 复制配置文件
cp ~/Library/Preferences/com.googlecode.iterm2.plist ~/dotfiles/iterm2/

# 如果使用动态配置
cp -r ~/Library/Application\ Support/iTerm2/DynamicProfiles ~/dotfiles/iterm2/
```

### 方法 3: 使用命令行导出

```bash
# 导出配置为 JSON 格式
defaults export com.googlecode.iterm2 ~/dotfiles/iterm2/iterm2-config.json

# 导出配置为 XML 格式
defaults export com.googlecode.iterm2 ~/dotfiles/iterm2/iterm2-config.xml
```

---

## 📥 导入/恢复配置

### 方法 1: 使用 iTerm2 内置功能（推荐）

1. 安装 iTerm2
2. 打开 iTerm2
3. 菜单栏 → **Preferences**
4. **General** → **Preferences**
5. 勾选 **Load preferences from a custom folder or URL**
6. 点击 **Browse** 选择配置文件所在目录
7. 重启 iTerm2

### 方法 2: 手动复制配置文件

```bash
# 关闭 iTerm2
killall iTerm2

# 备份现有配置（可选）
cp ~/Library/Preferences/com.googlecode.iterm2.plist \
   ~/Library/Preferences/com.googlecode.iterm2.plist.backup

# 恢复配置
cp ~/dotfiles/iterm2/com.googlecode.iterm2.plist ~/Library/Preferences/

# 如果有动态配置
mkdir -p ~/Library/Application\ Support/iTerm2/
cp -r ~/dotfiles/iterm2/DynamicProfiles ~/Library/Application\ Support/iTerm2/

# 启动 iTerm2
open -a iTerm
```

### 方法 3: 使用命令行导入

```bash
# 关闭 iTerm2
killall iTerm2

# 从 JSON/XML 导入
defaults import com.googlecode.iterm2 ~/dotfiles/iterm2/iterm2-config.json

# 重启 iTerm2
open -a iTerm
```

---

## 🎨 配置同步到 Git

### 设置自动同步

```bash
# 1. 创建配置目录
mkdir -p ~/dev/development-note/iterm2

# 2. 在 iTerm2 中设置配置文件夹
# Preferences → General → Preferences
# Load preferences from: ~/dev/development-note/iterm2

# 3. 保存当前设置
# 点击 "Save Current Settings to Folder"

# 4. 添加到 Git
cd ~/dev/development-note
git add iterm2/
git commit -m "Add iTerm2 configuration"
git push
```

### .gitignore 配置

```bash
# 在 iterm2 目录下创建 .gitignore
cat > ~/dev/development-note/iterm2/.gitignore << 'EOF'
# 忽略临时文件
*.tmp
*.lock

# 忽略日志
*.log
EOF
```

---

## 🚀 快速迁移脚本

### 导出脚本

创建 `iterm2-export.sh`：

```bash
#!/bin/bash
# iterm2-export.sh - 导出 iTerm2 配置

echo "🚀 Exporting iTerm2 configuration..."

# 创建目录
mkdir -p ~/dev/development-note/iterm2

# 如果 iTerm2 正在运行，提示用户
if pgrep -x "iTerm2" > /dev/null; then
    echo "⚠️  iTerm2 is running. Please close it first."
    exit 1
fi

# 复制配置文件
cp ~/Library/Preferences/com.googlecode.iterm2.plist \
   ~/dev/development-note/iterm2/

# 复制动态配置（如果存在）
if [ -d ~/Library/Application\ Support/iTerm2/DynamicProfiles ]; then
    cp -r ~/Library/Application\ Support/iTerm2/DynamicProfiles \
       ~/dev/development-note/iterm2/
fi

echo "✅ Export complete!"
echo "📁 Configuration saved to: ~/dev/development-note/iterm2/"
```

### 导入脚本

创建 `iterm2-import.sh`：

```bash
#!/bin/bash
# iterm2-import.sh - 导入 iTerm2 配置

echo "🚀 Importing iTerm2 configuration..."

# 检查配置文件是否存在
if [ ! -f ~/dev/development-note/iterm2/com.googlecode.iterm2.plist ]; then
    echo "❌ Configuration file not found!"
    exit 1
fi

# 关闭 iTerm2
if pgrep -x "iTerm2" > /dev/null; then
    echo "🛑 Closing iTerm2..."
    killall iTerm2
    sleep 2
fi

# 备份现有配置
if [ -f ~/Library/Preferences/com.googlecode.iterm2.plist ]; then
    echo "📦 Backing up existing configuration..."
    cp ~/Library/Preferences/com.googlecode.iterm2.plist \
       ~/Library/Preferences/com.googlecode.iterm2.plist.backup
fi

# 恢复配置
cp ~/dev/development-note/iterm2/com.googlecode.iterm2.plist \
   ~/Library/Preferences/

# 恢复动态配置
if [ -d ~/dev/development-note/iterm2/DynamicProfiles ]; then
    mkdir -p ~/Library/Application\ Support/iTerm2/
    cp -r ~/dev/development-note/iterm2/DynamicProfiles \
       ~/Library/Application\ Support/iTerm2/
fi

echo "✅ Import complete!"
echo "🚀 Starting iTerm2..."
open -a iTerm
```

### 使用脚本

```bash
# 赋予执行权限
chmod +x ~/dev/development-note/iterm2/iterm2-export.sh
chmod +x ~/dev/development-note/iterm2/iterm2-import.sh

# 导出配置
~/dev/development-note/iterm2/iterm2-export.sh

# 导入配置
~/dev/development-note/iterm2/iterm2-import.sh
```

---

## 🔧 常用快捷键

### 窗口管理

| 快捷键 | 功能 |
|--------|------|
| `Cmd + T` | 新建标签 |
| `Cmd + N` | 新建窗口 |
| `Cmd + D` | 垂直分屏 |
| `Cmd + Shift + D` | 水平分屏 |
| `Cmd + [/]` | 切换标签 |
| `Cmd + Option + 方向键` | 切换分屏 |
| `Cmd + W` | 关闭当前标签 |

### 编辑操作

| 快捷键 | 功能 |
|--------|------|
| `Cmd + F` | 查找 |
| `Cmd + C` | 复制 |
| `Cmd + V` | 粘贴 |
| `Cmd + K` | 清屏 |
| `Cmd + +/-` | 调整字体大小 |
| `Cmd + 0` | 重置字体大小 |

### 高级功能

| 快捷键 | 功能 |
|--------|------|
| `Cmd + Shift + H` | 粘贴历史 |
| `Cmd + Option + E` | 搜索所有标签 |
| `Cmd + Option + B` | 时间回溯 |
| `Cmd + /` | 查找光标 |

---

## 💡 推荐配置

### 基础优化

1. **启用模糊搜索**
   - Preferences → Profiles → Keys
   - 设置快捷键：`Cmd+F`

2. **配置自动补全**
   - Preferences → Profiles → Advanced
   - 启用 Semantic History

3. **设置热键窗口**
   - Preferences → Keys → Hotkey
   - 设置全局快捷键（如 `Option+Space`）

4. **优化复制粘贴**
   - Preferences → General → Selection
   - 勾选 "Copy to pasteboard on selection"

### 外观优化

**推荐字体：**
- Monaco (macOS 内置)
- Meslo LG (Powerline 字体)
- Fira Code (带连字)
- JetBrains Mono

**推荐配色方案：**
- Solarized Dark
- Dracula
- One Dark
- Gruvbox Dark

---

## ❓ 常见问题

### Q1: 配置没有生效？

```bash
# 1. 确保 iTerm2 已关闭
killall iTerm2

# 2. 清除缓存
rm -rf ~/Library/Caches/com.googlecode.iterm2

# 3. 重新导入配置
defaults import com.googlecode.iterm2 ~/path/to/config.plist

# 4. 重启 iTerm2
open -a iTerm
```

### Q2: 配置文件同步冲突？

使用 iTerm2 的自动同步功能，避免手动复制：
- Preferences → General → Preferences
- 启用 "Load preferences from a custom folder"

### Q3: 如何重置配置？

```bash
# 关闭 iTerm2
killall iTerm2

# 删除配置文件
rm ~/Library/Preferences/com.googlecode.iterm2.plist

# 重启 iTerm2（会自动创建默认配置）
open -a iTerm
```

### Q4: 字体显示异常？

```bash
# 安装 Nerd Fonts
brew tap homebrew/cask-fonts
brew install font-meslo-lg-nerd-font

# 在 iTerm2 中设置
# Preferences → Profiles → Text → Font
# 选择 "Meslo LG" 或其他 Nerd Font
```

---

## 📚 相关资源

### 官方资源
- **官网**: https://iterm2.com
- **文档**: https://iterm2.com/documentation.html
- **GitHub**: https://github.com/gnachman/iTerm2

### 配色方案
- **iTerm2 Color Schemes**: https://iterm2colorschemes.com
- **Dracula**: https://draculatheme.com/iterm
- **Solarized**: https://github.com/altercation/solarized

### 字体推荐
- **Nerd Fonts**: https://www.nerdfonts.com
- **Powerline Fonts**: https://github.com/powerline/fonts
- **FiraCode**: https://github.com/tonsky/FiraCode

---

## 🎉 总结

iTerm2 配置管理的关键点：
- 📁 **使用内置同步** - 最可靠的方式
- 🔄 **Git 版本控制** - 便于跨设备同步
- 🔒 **定期备份** - 避免配置丢失
- 🎨 **个性化设置** - 提高工作效率
- 🚀 **自动化脚本** - 简化迁移流程

**配置好 iTerm2，让终端体验更美好！** 🚀✨
