# Tmux 使用笔记

## 简介

Tmux (Terminal Multiplexer) 是一个终端复用工具，允许在单个终端窗口中创建、访问和控制多个终端会话。

## 核心概念

- **Session (会话)**: 一个 tmux 工作环境的集合
- **Window (窗口)**: 类似于浏览器的标签页，一个会话可以有多个窗口
- **Pane (窗格)**: 窗口的分割区域，可以同时显示多个终端

## 基本操作

### 会话管理

```bash
# 创建新会话
tmux new -s session-name

# 列出所有会话
tmux ls

# 附加到已存在的会话
tmux attach -t session-name
# 或简写
tmux a -t session-name

# 分离当前会话（在 tmux 内部使用）
Ctrl+b d

# 删除会话
tmux kill-session -t session-name

# 重命名会话
tmux rename-session -t old-name new-name
```

### 窗口操作

默认前缀键是 `Ctrl+b`，以下命令都需要先按前缀键：

```bash
c       # 创建新窗口
,       # 重命名当前窗口
w       # 列出所有窗口
n       # 切换到下一个窗口
p       # 切换到上一个窗口
0-9     # 切换到指定编号的窗口
&       # 关闭当前窗口
```

### 窗格操作

```bash
%       # 垂直分割窗格
"       # 水平分割窗格
o       # 切换到下一个窗格
;       # 切换到上一个活动窗格
x       # 关闭当前窗格
z       # 最大化/还原当前窗格
{       # 向前交换窗格
}       # 向后交换窗格
空格键   # 在预设的窗格布局中切换
q       # 显示窗格编号
方向键   # 移动到指定方向的窗格
```

### 调整窗格大小

```bash
Ctrl+b :resize-pane -D  # 向下调整
Ctrl+b :resize-pane -U  # 向上调整
Ctrl+b :resize-pane -L  # 向左调整
Ctrl+b :resize-pane -R  # 向右调整

# 也可以指定调整的行数
Ctrl+b :resize-pane -D 10
```

## 配置文件

Tmux 的配置文件位于 `~/.tmux.conf`

### 常用配置示例

```bash
# 设置前缀键为 Ctrl+a
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# 启用鼠标支持
set -g mouse on

# 设置窗格索引从 1 开始
set -g base-index 1
setw -g pane-base-index 1

# 快速重新加载配置文件
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# 设置更好的分割快捷键
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# 使用 vim 风格的窗格切换
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# 增加历史记录限制
set -g history-limit 10000

# 设置终端颜色
set -g default-terminal "screen-256color"
```

## 复制模式

```bash
[       # 进入复制模式
空格键   # 开始选择
Enter   # 复制选择的内容
]       # 粘贴复制的内容
```

### Vim 风格的复制模式配置

```bash
# 在 ~/.tmux.conf 中添加
setw -g mode-keys vi
bind-key -T copy-mode-vi v send -X begin-selection
bind-key -T copy-mode-vi y send -X copy-selection-and-cancel
```

## 常用技巧

### 快速查找会话

```bash
# 使用模糊查找（需要配合 fzf）
tmux ls | fzf | awk '{print $1}' | sed 's/://' | xargs tmux attach -t
```

### 同步窗格

在多个窗格中同时输入相同的命令：

```bash
Ctrl+b :setw synchronize-panes on   # 开启同步
Ctrl+b :setw synchronize-panes off  # 关闭同步
```

### 保存和恢复会话

使用 tmux-resurrect 插件：

```bash
# 安装 TPM (Tmux Plugin Manager)
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# 在 ~/.tmux.conf 中添加
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-resurrect'

# 运行插件管理器
run '~/.tmux/plugins/tpm/tpm'

# 使用快捷键
Ctrl+b Ctrl+s  # 保存会话
Ctrl+b Ctrl+r  # 恢复会话
```

## 常见问题

### 颜色显示不正常

在 `~/.bashrc` 或 `~/.zshrc` 中添加：

```bash
export TERM=xterm-256color
```

### 鼠标滚动不工作

确保在配置文件中启用了鼠标支持：

```bash
set -g mouse on
```

### 系统剪贴板集成

在 macOS 上：

```bash
# 安装 reattach-to-user-namespace
brew install reattach-to-user-namespace

# 在 ~/.tmux.conf 中添加
set -g default-command "reattach-to-user-namespace -l $SHELL"
```

## 参考资源

- [Tmux 官方文档](https://github.com/tmux/tmux/wiki)
- [Tmux Cheat Sheet](https://tmuxcheatsheet.com/)
- [Awesome Tmux](https://github.com/rothgar/awesome-tmux)
