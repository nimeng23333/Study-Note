---
title: 自建 VPN 保姆级教程
---


> 从零到一搭建属于你自己的 VPN 节点 · 基于真实搭建经验整理

📝 整理：基于个人实操经验 📅 日期：2026-05 🎯 目标读者：完全没接触过 VPS 的小白 💰 一年成本：约 ¥160-260（VPS $21.99-$35.99/年）

---

## 💡 为什么自建

### 自建 vs 机场对比

|   |   |   |
|---|---|---|
|项目|机场（订阅）|自建（本教程）|
|月费|30-100 元|0 元（年付 35 美元 ≈ 19 元/月）|
|流量|100-300GB/月|**5TB/月（用不完）**|
|节点|共享多人|**独享**|
|隐私|运营商能看你流量|完全自己控制|
|抗封锁|看运营商技术|**Reality 协议（最强）**|
|跑路风险|⚠️ 有|❌ 无|
|给家人用|多端付费|**免费分享**|
|维护成本|0|偶尔 10 分钟|

---

## 🛒 第一步：购买 RackNerd VPS

### 购买链接

🔗 **官网套餐页**：**https://www.racknerd.com/specials/**

打开链接，会看到几个套餐：

|   |   |   |   |   |
|---|---|---|---|---|
|套餐|配置|流量|年费|适合|
|**1 GB KVM VPS**|1 核 / 1GB / 20GB SSD|3 TB/月|**$21.99**|单人轻度使用|
|**2 GB KVM VPS** ⭐|2 核 / 2GB / 35GB SSD|5 TB/月|**$35.99**|个人 + 家庭使用（推荐）|
|**4 GB KVM VPS**|3 核 / 4GB / 60GB SSD|7 TB/月|**$59.99**|团队/办公室|
|6 GB KVM VPS|6 核 / 6GB / 100GB|12 TB/月|$89.99|重度使用|

### 选哪个套餐？

- **个人轻度使用** → **1 GB 版** 就够（看推特、Google、写代码）
    
- **想给家人用 / 看视频多** → **2 GB 版**（最推荐，性价比之王）
    
- **团队/办公室共享** → **4 GB 或更高**
    

> ⚠️ **重要提示**：流量都很充足，不必为了省 14 美元买 1GB 版导致后期不够用。**多 14 美元买 2GB 版**，性能更好、流量更多、用得更久。

### 关于折扣码

VPS 套餐**已经是促销价**，普通用户**无需输入折扣码**。

如果你买**专用服务器（Dedicated Server，月费 60+ 美元的那种）**，可以用：

```Plaintext
15OFFDEDI    →  专用服务器 15% off (终身)
```

> 99% 的人用不到专用服务器，VPS 完全够用。我自己没有用

### 购买步骤

1. **打开链接**：https://www.racknerd.com/specials/
    
2. **找你想买的套餐**，点 **"Order Now"**
    
3. **配置选择**：
    
    1. **Location**: 选 **`Los Angeles, CA`** （洛杉矶，到国内最快）
        
    2. **Operating System**: 选 **`Debian 13 64 Bit`**（推荐）或者 `Ubuntu 22.04 64 Bit`
        
    3. **Hostname**: 随便填，比如 `my-vps`
        
4. 点 **"Continue"**
    
5. **填账号信息**：
    
    1. **Email**: 常用邮箱（用来收 VPS 账号信息）
        
    2. **密码**: 设一个**强密码**（登录 RackNerd 后台用）
        
    3. **国家**: 选 China 或 US 都可以
        
    4. **填地址等基本信息**
        
6. **选支付方式** ⭐ 重点！
    

### 💳 支付方式（**支持支付宝！**）

RackNerd 官方支持以下支付方式：

|   |   |   |
|---|---|---|
|支付方式|推荐度|说明|
|**🥇 支付宝（Alipay）**|⭐⭐⭐⭐⭐|**强烈推荐，国内用户首选！直接扫码支付**|
|信用卡（Visa/Master/Amex/Discover）|⭐⭐⭐⭐|有外币卡的用这个|
|银联（UnionPay）|⭐⭐⭐⭐|国内银行卡可用|
|PayPal|⭐⭐⭐|有 PayPal 账号的用|
|加密货币（USDT/BTC/ETH 等）|⭐⭐⭐|想匿名的用|
|微信支付|❌|不支持|

**对于国内用户来说，强烈推荐用支付宝！** 直接扫码秒付，无需信用卡。

### 支付宝支付步骤

1. 在结账页面，**Payment Method** 选 **`Alipay`**
    
2. 点 **"Complete Order"**
    
3. 跳转到支付宝支付页面，**用手机扫二维码**
    
4. 支付宝完成支付（按当前汇率结算成人民币，比如 $35.99 ≈ ¥260）
    

### 等待开通

- 一般 **5-30 分钟**会收到开通邮件
    
- 邮件包含：
    
    - **VPS IP 地址**（一串数字，比如 `198.51.100.42`）
        
    - **root 密码**（一长串随机字符）
        
    - **后台登录链接**（管理 VPS 的网页，类似 `https://my.racknerd.com`）
        
    - **VPS 控制面板链接**（重启/重装系统的网页，类似 `https://lax.racknerd.com:5656`）
        

⚠️ **务必保存好这封邮件**！里面所有信息都很重要。

---

## 🔑 第二步：登录 VPS

### 准备 SSH 工具

#### Mac 用户

直接打开 **终端**（Mac 自带，按 `Cmd+空格` 搜 "终端" 即可）。

#### Windows 用户

下载 **PuTTY**：https://www.putty.org

或者用 Windows 11 自带的 **PowerShell**（搜索 PowerShell 打开）。

### 第一次 SSH 登录

打开终端，输入以下命令（把 `198.51.100.42` 换成**你自己邮件里的 IP**）：

```Bash
ssh root@198.51.100.42
```

第一次连接会问：

```Plaintext
The authenticity of host '198.51.100.42' can't be established.
Are you sure you want to continue connecting (yes/no)?
```

输入 `yes` 回车。

然后会提示输入密码：

```Plaintext
root@198.51.100.42's password:
```

**复制邮件里给的密码**，粘贴进去（终端粘贴后看不到字符，是正常的），回车。

### 修改 root 密码

邮件给的密码很难记，建议改成自己记得住的强密码：

```Bash
passwd
```

会提示输入新密码两次（输入时同样看不到字符）：

```Plaintext
New password:
Retype new password:
passwd: password updated successfully
```

✅ **记住新密码**！以后登录用这个。

### 更新系统（推荐）

```Bash
apt update && apt upgrade -y
```

等几分钟跑完。

---

## 📦 第三步：一键安装 3x-ui 面板

我们用 **3x-ui** 面板，开源免费，图形化管理节点，对小白超友好。

### 一键安装命令

在 VPS 终端执行：

```Bash
bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)
```

按提示一路回车（或者输入 `y`）。

会问几个问题：

```Plaintext
Would you like to customize the panel access settings? (y/n)
```

**输入** **`y`**（自定义更安全）：

```Plaintext
Please set up your username: 输入用户名,比如 myadmin
Please set up your password: 输入面板密码,比如 MyStr0ngPass2026
Please set up the panel port: 输入端口,推荐 2053
```

⚠️ **重要**：

- 用户名密码**记牢**！以后登录面板用
    
- 端口**记牢**！默认 2053 也行
    

### 安装完成验证

安装成功会显示：

```Plaintext
3x-ui has been installed successfully!
Panel URL: https://你的VPS_IP:2053/
Username: myadmin
Password: MyStr0ngPass2026
```

✅ **截图保存这些信息！**

### 防火墙放行端口（如果系统装了 ufw）

```Bash
ufw allow 22       # SSH
ufw allow 2053     # 面板
ufw allow 443      # 节点端口（后面要用）
ufw allow 2096     # 订阅端口（后面要用）
```

如果提示 `ufw: command not found`，**直接跳过**这步（RackNerd 默认不装 ufw，端口都是开的）。

---

## ⚡ 第四步：开启 BBR 加速

BBR 是 Google 开发的网络加速技术，**能让代理速度提升 30-100%**，必开。

### 一键开启

```Bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

### 验证

```Bash
sysctl net.ipv4.tcp_congestion_control
```

应该显示：

```Plaintext
net.ipv4.tcp_congestion_control = bbr
```

✅ 开启成功。

---

## 🌐 第五步：登录面板创建 Reality 节点

### 登录 3x-ui 面板

浏览器打开（把 IP 换成你自己的）：

```Plaintext
https://198.51.100.42:2053
```

⚠️ **注意是 https** 不是 http！

会显示 **"您的连接不是私密连接"** 警告（因为没买 SSL 证书，正常现象），点 **"高级"** → **"继续访问"**。

输入第三步设置的**用户名密码**登录。

### 创建 Reality 入站

进入面板后：

1. 左侧菜单 **"入站列表"** → 点击 **"添加入站"**
    
2. **基础配置**：
    
    1. **备注**: `myreality`（随便起名，比如 `自用` `家用` 都行）
        
    2. **协议**: `vless`
        
    3. **监听 IP**: 留空
        
    4. **端口**: `443` （**伪装成 HTTPS,最隐蔽**）
        
    5. **总流量**: `0` （0 表示不限）
        
    6. **过期时间**: `0` （0 表示不过期）
        
3. **客户端配置**：
    
    1. 找到 **"客户端"** 区域
        
    2. **Email/ID**: 输入一个标识，比如 `me`
        
    3. **Flow**: 必须选 **`xtls-rprx-vision`** ⭐ 关键！不选这个会很慢
        
4. **传输配置**：
    
    1. **传输方式**: `tcp`
        
    2. **TLS**: 选 **`reality`**
        
5. **Reality 设置**（最关键）：
    
    1. **uTLS**: 选 `chrome`
        
    2. **Server Names (SNI)**: 填 `www.amazon.com`（伪装成访问 Amazon）
        
    3. **目标地址（Dest）**: 填 `www.amazon.com:443`
        
    4. **公私钥**: 点旁边的 **"获取新证书"** 按钮，自动生成
        
    5. **Short IDs**: 同样点 **"获取新证书"** 自动生成
        
6. 点 **"创建"** 按钮
    

### 获取节点链接

创建成功后，回到入站列表：

1. 找到刚创建的 `myreality`
    
2. 点击展开 → 点 **"操作"** → **"二维码"** 或 **"链接"**
    
3. 会看到一个 `vless://` 开头的长字符串
    

✅ 这就是你的**节点链接**！妥善保存。

### 重要参数记录

把以下信息记到备忘录（后面客户端配置要用）：

```Plaintext
VPS IP:     198.51.100.42      ← 你邮件里的真实 IP
节点端口:   443
UUID:       面板里看到的客户端 UUID
Flow:       xtls-rprx-vision
SNI:        www.amazon.com
公钥:       面板生成的 Public Key
ShortID:    面板生成的 Short ID
指纹:       chrome
```

---

## 🔗 第六步：开启订阅服务

订阅服务可以**生成一个链接给客户端**，未来更新节点不用每次重新导入。

### 设置订阅服务

3x-ui 面板 → 左侧菜单 **"面板设置"** → **"订阅设置"**

配置：

```Plaintext
启用订阅服务: 开启 ✅
订阅监听域名: 留空
订阅端口: 2096
订阅路径: /sub/
启用 HTTPS: 关闭 (除非你买了域名+证书)
```

点 **"保存"** → 点 **"重启面板"**。

### 获取订阅链接

回到 **"入站列表"** → 找到你的 `myreality`：

1. 展开节点
    
2. 在客户端那一栏，找到你创建的 `me`
    
3. 点 **"二维码"** 或 **"操作"** 找到订阅链接，格式类似：
    

```Plaintext
http://198.51.100.42:2096/sub/xxxxxxxxxxxxxxxx
```

⚠️ **注意是 http 不是 https**（因为没买证书）

✅ 这个链接给手机/Mac 客户端用！

---

## 💻 第七步：Mac 端配置 Clash Verge

### 下载 Clash Verge

🔗 https://github.com/clash-verge-rev/clash-verge-rev/releases

下载 Mac 版：

- **苹果M系列 芯片** → 选带 `aarch64` 的
    
- **Intel 芯片** → 选带 `x64` 的
    

安装后打开。

### 导入配置（两种方式）

#### 方式 A：用订阅链接（推荐新手）

1. Clash Verge → 左侧 **"订阅"**
    
2. 顶部输入框粘贴订阅链接：`http://198.51.100.42:2096/sub/xxx`
    
3. 点 **"导入"**
    
4. 点导入的配置卡片，启用它
    

#### 方式 B：用完整 yaml 文件（推荐老手）

参考本教程末尾的"完整 Clash Verge 配置文件"段落，下载现成的 yaml，改成你自己的节点参数即可。

**这个 yaml 包含 432 条规则**，覆盖国内国外所有常见网站，体验接近机场。

### 开启 TUN 模式（关键！）

Clash Verge → **设置** → **Clash 设置**：

- ✅ 开启 **"TUN 模式"**
    
- ✅ TUN 栈选 **`system`** （**性能最好**，不要选 gvisor）
    
- ✅ 开启 **"系统代理"**
    

第一次开 TUN 会要求 Mac 密码授权，输入即可。

### 测试

打开 Chrome，访问 https://google.com 看能不能开。

如果能开 → 🎉 成功！

---

## 📱 第八步：iPhone 端配置 Shadowrocket

### 下载 Shadowrocket

App Store **美区**搜 `Shadowrocket`，付费 $2.99。

> ⚠️ 国区下不了，需要美区 Apple ID。可以问朋友借/在某宝买现成账号（5-10 元）。

### 导入节点

打开 Shadowrocket → 右上角 **+** → 选 **"扫描二维码"**：

1. 在 3x-ui 面板上找你节点的二维码
    
2. iPhone 扫描
    

或者：

1. 手动复制 `vless://` 链接
    
2. 在 Shadowrocket 打开 → 自动弹窗提示"是否添加节点"
    

### 启用代理

Shadowrocket 主界面 → 顶部开关打开

第一次会要求允许添加 VPN 配置，点允许。

### 测试

Safari 打开 https://google.com 看能不能开。

---

## 🛠️ 第九步：日常应用代理配置（重要！）

⚠️ **这一步是新手最容易忽略但最重要的！**

不是开了 Clash Verge 就完事了。**很多应用不会自动走系统代理**，需要单独配置。

### 浏览器（基本自动）

✅ Chrome / Safari / Edge / Firefox 都会自动走系统代理。

但 Chrome 需要注意两个设置（**否则翻译插件不工作**）：

#### 关掉 Chrome DoH

```Plaintext
Chrome 地址栏输入: chrome://settings/security
→ 滚到底部
→ 关闭 "使用安全 DNS"
```

#### 禁用 QUIC

```Plaintext
Chrome 地址栏输入: chrome://flags
→ 搜 "quic"
→ "Experimental QUIC protocol" 改成 Disabled
→ 重启 Chrome
```

### 命令行工具（iTerm/Terminal）

#### 方法 A：用 alias（推荐）

把以下加到 `~/.zshrc`：

```Bash
# Clash Verge 默认端口 7897
alias verge_on='export http_proxy=http://127.0.0.1:7897 https_proxy=http://127.0.0.1:7897 all_proxy=socks5://127.0.0.1:7897 && echo "✅ 终端代理已开"'

# 关闭代理
alias proxy_off='unset http_proxy https_proxy all_proxy HTTP_PROXY HTTPS_PROXY ALL_PROXY && echo "❌ 终端代理已关"'

# 查询当前出口 IP
alias myip='curl -s https://ipinfo.io | grep -E "\"(ip|city|country)\""'
```

加完执行 `source ~/.zshrc` 生效。

用法：

```Bash
verge_on   # 开终端代理
git clone xxx
proxy_off  # 关终端代理
myip       # 看当前出口 IP
```

#### 方法 B：开 TUN 模式（自动）

如果你 Clash Verge 开了 TUN 模式，**所有命令行工具自动走代理**，不需要手动 export。

### Cursor（AI 编程）

```Plaintext
Cursor → Cmd+, → 搜 "proxy"
→ Http: Proxy: 留空 (依赖系统代理)
```

或者强制配置：

```Plaintext
Http: Proxy: http://127.0.0.1:7897
```

### Claude Code CLI

如果遇到 `ECONNREFUSED` 错误，加 alias：

```Bash
# 在 ~/.zshrc 添加
alias claude='http_proxy=http://127.0.0.1:7897 https_proxy=http://127.0.0.1:7897 all_proxy=socks5://127.0.0.1:7897 claude'
```

### 微信

✅ 微信会自动走 TUN 模式或系统代理，正常使用即可。

---

## 📋 完整 Clash Verge 配置文件

完整 yaml 配置文件可以参考你之前配置用AI帮你生成一份你自己专属的，我自己生成 `luban-demo.yaml`，仅供参考

暂时无法在飞书文档外展示此内容
![](note_images/luban-demo.yaml)
包含：

- ✅ 几百条 条规则（国内国外完整覆盖）
    
- ✅ 微信全家桶白名单
    
- ✅ AI 服务专组（ChatGPT/Claude/Cursor）
    
- ✅ DNS 防污染配置
    
- ✅ TUN system stack 优化
    

⚠️ 使用时把里面的**节点配置**改成你自己的：

```YAML
proxies:
  - name: "🚀 我的节点"
    type: vless
    server: 198.51.100.42      ← 改成你的 VPS IP
    port: 443
    uuid: 你的UUID             ← 改成面板里的 UUID
    network: tcp
    udp: true
    tls: true
    flow: xtls-rprx-vision
    servername: www.amazon.com
    reality-opts:
      public-key: 你的公钥      ← 改成面板生成的 Public Key
      short-id: 你的ShortID     ← 改成面板生成的 Short ID
    client-fingerprint: chrome
```

---

## ❓ 常见问题 Q&A

### Q1：可以用支付宝支付吗？

**可以！** RackNerd 官方支持支付宝（Alipay/支付宝），结账时选 `Alipay` 即可，扫码秒付。

### Q2：为什么我自己搭的速度比机场慢？

**原因**：

1. 你只有 1 个节点，机场有几十个节点（自动选最快）
    
2. 你没开 BBR 加速（按本教程开启）
    
3. 高峰期你的 VPS 出口拥堵
    
4. TUN stack 用的是 gvisor（必须改成 system）
    

**解决**：

- ✅ 开启 BBR
    
- ✅ 选洛杉矶机房（不要选其他出口紧张的地区）
    
- ✅ TUN stack 改 system
    
- ✅ 用 Reality 协议（比 V2Ray TLS 快）
    

### Q3：Reality 协议是什么？为什么选它？

**Reality** 是 2023 年推出的最新协议，**伪装成访问真实网站**（比如 amazon.com）。

GFW 的检测逻辑：

- 看到流量像访问 Amazon → ✅ 放行
    
- 实际上你的代理流量被加密在里面
    

**优势**：

- 抗封锁能力**最强**（目前几乎不可能被识别）
    
- 性能比 V2Ray + TLS 好
    
- 不需要域名和证书（省钱省事）
    

### Q4：5TB/月用得完吗？

**完全用不完。**

参考用量：

- 每天看 1 小时 4K YouTube：约 7GB/天 = 210GB/月
    
- 浏览网页 + 偶尔看视频：约 100GB/月
    
- 重度用户（大量视频/下载）：约 500GB/月
    

**5TB = 5000GB**，足够 10 个人重度使用。

### Q5：可以分享给家人朋友吗？

**完全可以！** 自建的最大优势就是分享免费。

3x-ui 面板里：

- 给每个家人创建独立的客户端
    
- 设置流量限制（避免某人用太多）
    
- 给每人独立的订阅链接
    

操作：

1. 编辑入站节点 → 客户端 → 添加新客户端
    
2. 设置 Email、UUID、流量上限
    
3. 拿订阅链接给对方
    

### Q6：被封了怎么办？

Reality 节点几乎不会被识别（伪装太逼真）。但万一被封，有以下方案：

#### 方案 1：换端口

- 3x-ui 面板里把端口从 443 改成别的（比如 8443）
    

#### 方案 2：换协议

- 创建一个 Trojan 节点作为备用
    

#### 方案 3：换 IP

- RackNerd 后台可以付几美元换 IP
    

#### 方案 4：实在不行重买 VPS

- 一年 35 美元，重买不心疼
    

### Q7：每月续费麻烦吗？

**年付不需要月费**！我们买的是年付套餐，**一年只续 1 次**。

提前 1-2 周收到提醒邮件，在 RackNerd 后台一键续费即可。**续费价格和首次购买一样**，不会涨价。

### Q8：Mac 关 TUN 模式后某些 App 不能上网？

正常。**TUN 模式负责接管所有流量**。

关 TUN 后：

- 浏览器：✅ 走系统代理
    
- Telegram：❌ 需要单独配 SOCKS5
    
- iTerm：❌ 需要 export 代理（按本教程 命令行工具 段）
    
- 微信：✅ 走系统代理
    

**建议日常保持 TUN 开着**，省心。

### Q9：TUN 模式开了但微信朋友圈很慢？

**TUN stack 是 gvisor（性能差）**，必须改成 system。

**解决**：

- Clash Verge → 设置 → TUN → Stack → 改成 `system`
    
- 或者 yaml 里加 `tun: stack: system`
    
- 重启 Clash Verge，授权 Mac 密码
    

### Q10：windows 用什么客户端？

推荐 **Clash Verge**，跟 Mac 版完全一样的界面。

下载：https://github.com/clash-verge-rev/clash-verge-rev/releases

### Q11：iPhone 美区账号怎么搞？

- 自己注册：去 Apple ID 网站 → 切换地区 → 选 United States → 地址用美国地址生成器
    
- 或者某宝买现成账号 5-10 元
    
- 借朋友的（最简单）
    

### Q12：买完后多久能用？

- VPS：**5-30 分钟**自动开通（看邮件）
    
- 整体搭建：第一次跟教程做约 **2 小时**（含等待）
    
- 熟练后：**30 分钟**就能搞定
    

---

## 踩坑记录

> 以下 3 个坑是部署阶段最容易踩的，**用本教程的完美 yaml 已经避开了大部分其他坑**（朋友圈、Telegram、Cursor、DNS 污染等），但这 3 个跟"操作步骤"有关，必须人工注意。

### 坑 1：Reality 节点 Flow 必须 `xtls-rprx-vision`

**症状**：节点能连，但速度极慢甚至不通。

**原因**：3x-ui 面板创建节点时 Flow 字段忘了选 `xtls-rprx-vision`。

**解决**：编辑节点 → 客户端 → Flow 字段选 `xtls-rprx-vision`。

### 坑 2：订阅链接必须用 http 不是 https

**症状**：订阅链接导入失败。

**原因**：3x-ui 默认订阅服务没开 HTTPS（除非你买了 SSL 证书）。

**解决**：链接前缀用 `http://` 而不是 `https://`。

### 坑 3：Chrome 翻译插件不工作（最难发现）

**症状**：Chrome 翻译插件显示 "无法翻译此网页"。

**原因 1（最隐蔽）**：`/etc/hosts` 里有奇怪的映射（之前装过的某些工具偷偷改的）

```Bash
# 检查 hosts 文件
cat /etc/hosts | grep translate

# 如果有 "180.163.151.34 translate.googleapis.com" 这种,删掉:
sudo sed -i '' '/translate.googleapis.com/d' /etc/hosts
sudo dscacheutil -flushcache
```

**原因 2**：Chrome 的 DoH 没关

- `chrome://settings/security` → 关 "使用安全 DNS"
    

**原因 3**：Chrome QUIC 协议

- `chrome://flags` → 搜 quic → Disabled → 重启
    

---

## 给家人朋友分享

### 给家人开账号

**步骤**：

1. 登录 3x-ui 面板
    
2. 入站列表 → 找到 Reality 节点 → 编辑
    
3. 客户端 → 添加新客户端：
    
    1. Email: `dad`（或其他标识）
        
    2. 总流量: `500` GB（限制流量，避免某人用太多）
        
    3. 过期时间: 设个长期日期
        
4. 复制这个新客户端的**订阅链接**
    
5. 微信发给家人
    

### 家人怎么用

#### Mac

- 装 Clash Verge
    
- 订阅 → 导入链接 → 启用
    

#### iPhone

- 装 Shadowrocket（美区）
    
- 扫描你给的二维码
    

#### Windows

- 装 Clash Verge
    
- 同 Mac 操作
    

### 流量监控

3x-ui 面板可以看每个客户端用了多少流量，方便了解使用情况。

---

## 完成！

恭喜！按本教程操作，你应该已经搭好了一套**专业级**的科学上网方案：

✅ 自己的 VPS（不依赖第三方机场） ✅ Reality 协议（最强抗封锁） ✅ 智能分流（国内直连 + 国外代理） ✅ 多端覆盖（Mac、iPhone、Windows） ✅ 大流量（用不完） ✅ 可以分享给家人

**总成本**：

- VPS：$$21.99 -$$35.99/年 ≈ ¥160 - ¥260
    
- 客户端：免费
    
- 维护：偶尔 10 分钟
    

**对比机场**：

- 机场：30-100 元/月 = 360-1200 元/年
    
- 自建：160-260 元/年
    

---

## 排查问题

### 自助排查

按这个顺序：

1. **看 Clash Verge 连接列表**
    
    1. 搜索访问的域名 → 看走了什么节点 → 看是否成功
        
2. **命令行测试**
    

```Bash
# 测试节点
curl -I --max-time 5 -x http://127.0.0.1:7897 https://www.google.com

# 测试当前出口 IP
curl -s https://ipinfo.io
```

3. **检查 yaml 配置**
    

```Bash
# 验证 yaml 语法
python3 -c "import yaml; yaml.safe_load(open('config.yaml'))"
```

### 常见错误对照表

|   |   |
|---|---|
|现象|可能原因|
|完全连不上|VPS 挂了 / 端口被封 / yaml 错误|
|能连但慢|节点拥堵 / 没开 BBR / TUN stack 是 gvisor|
|部分网站打不开|DNS 污染 / 规则不对 / hosts 文件作怪|
|浏览器能用别的不能|应用没配代理（按教程"第九步"配置）|
|Chrome 翻译失效|见踩坑 3|

---

## 🔐 安全提醒

### 必做

- ✅ 修改默认 root 密码（强密码）
    
- ✅ 修改面板密码（强密码）
    
- ✅ 启用面板 2FA
    
- ✅ 不要把订阅链接发到公开网络
    
- ✅ 不要把 yaml 文件 commit 到 GitHub（含 UUID、公钥）
    

### 不要做

- ❌ 不要在 zshrc 里写 API key（用 macOS keychain 存）
    
- ❌ 不要把 root 密码告诉别人
    
- ❌ 不要分享给陌生人（只给信任的家人朋友）
    
- ❌ 不要用代理做违法的事
    

  

---

## 延伸阅读

- 3x-ui 官方文档：https://github.com/MHSanaei/3x-ui
    
- Clash Verge 文档：https://clash-verge-rev.github.io
    
- Reality 协议原理：https://github.com/XTLS/REALITY
    

---