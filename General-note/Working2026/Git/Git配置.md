---
title: Git配置
---

a.本来macOS应该自带git，如果没有那就安装clt，并检查git版本

```Plain
xcode-select --install
git --version
```

b. 安装Homebrew（如果需要）

```Plain
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 如果git clone没有弹出私人仓库的登陆信息，需要在vscode正确配置git用户信息

```Plain
# 设置用户名（使用你的GitLab用户名）
git config --global user.name "你的GitLab用户名"

# 设置邮箱（使用GitLab注册邮箱）
git config --global user.email "你的GitLab邮箱"

# 验证配置
git config --global user.name
git config --global user.email

# 设置正确的macOS凭证助手
git config --global credential.helper osxkeychain
```