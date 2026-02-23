---
title: node版本控制
---


用nvm控制node版本，可以在不同的terminal运行不同版本的node

### 1.确保有 `.zshrc`

`touch ~/.zshrc`

---

### 2.用官方脚本安装 nvm

在终端执行：

`curl -o-` `https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh` `| bash`

这一步跑完后，脚本一般会自动往你的 `~/.zshrc` 里加几行配置。

---

### 3.把 nvm 加到 zsh 环境里

执行这两行：

`echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc` `echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.zshrc`

然后让配置生效：

`source ~/.zshrc`

测试一下：

`nvm --version`

如果这里能输出类似 `0.39.7`，说明 nvm 已经 OK 了

---

### 4.装 Node 18 和 16

`nvm install 18.18.2` `nvm install 16.20.0`

然后可以切换试一下：

`nvm use 18` `node -v # 应该是 v18.x.x` `nvm use 16` `node -v # 应该是 v16.x.x`

---

### 5.vscode终端加载配置

如果在vscode的终端不能用nvm命令，重复terminal里的配置，`source ~/.zshrc`