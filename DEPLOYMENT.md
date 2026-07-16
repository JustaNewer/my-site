# 从购买域名和服务器开始部署个人网站

本文是一份从零开始的完整上线指南。完成后，在浏览器输入你购买的域名，例如 `https://jackye.com`，就能访问当前个人网站。

主路线采用：

- 域名注册和 DNS：腾讯云域名注册 + DNSPod
- 服务器：腾讯云轻量应用服务器，中国香港地域
- 操作系统：Ubuntu 24.04 LTS
- Web 服务器：Nginx
- 文件上传：Windows PowerShell + SSH/SCP
- HTTPS：Let's Encrypt + Certbot

选择中国香港服务器的主要原因是可以较快上线，不需要等待中国大陆 ICP 备案。如果选择中国大陆服务器，必须先完成 ICP 备案，具体区别见本文最后一章。

---

## 一、开始前准备

准备以下内容：

1. 一个可以完成实名认证的腾讯云账号。
2. 身份证、实名手机号和常用邮箱。
3. 一张可以付款的银行卡，或微信、支付宝等腾讯云支持的支付方式。
4. 当前网站文件夹：

```text
D:\Users\yxh\Desktop\yxhBlog
```

5. 想购买的域名候选，例如：

```text
jackye.com
oxjackye.com
realjackye.com
jackye.me
```

域名是按年租用的，不是永久买断。购买时除了查看第一年价格，还要查看续费价格，并建议开启自动续费。

---

## 二、选择大陆还是香港服务器

### 推荐：腾讯云中国香港服务器

优点：

- 购买后可以直接部署和解析域名。
- 通常不需要中国大陆 ICP 备案。
- 中国大陆、香港及海外用户都能访问。
- 操作步骤较少，适合个人介绍网站。

可能的缺点：

- 中国大陆访问延迟通常高于大陆服务器。
- 网络质量会随线路和运营商变化。
- 价格和套餐流量与大陆服务器不同。

### 备选：中国大陆服务器

优点：大陆访问速度和稳定性通常更好。

限制：使用中国大陆服务器公开提供网站前，必须先完成 ICP 备案并取得备案号。腾讯云当前要求用于备案的轻量应用服务器位于中国大陆、包年包月购买至少 3 个月，并满足剩余有效期等条件。

如果你只是希望先把个人网站上线，建议先选择中国香港地域。以后确实需要大陆线路，再迁移服务器并办理备案。

---

## 三、注册并实名认证腾讯云账号

1. 打开 [腾讯云](https://cloud.tencent.com/)。
2. 点击右上角 `登录`，使用微信、QQ、邮箱或其他支持方式创建账号。
3. 登录后点击右上角头像，进入 `账号信息` 或 `实名认证`。
4. 选择个人认证。
5. 根据页面要求提交姓名、身份证和人脸验证等信息。
6. 等待账号实名认证完成。

域名注册还需要域名实名信息模板。账号实名认证和域名实名认证是两个相关但不同的步骤。

---

## 四、创建域名实名信息模板

1. 登录腾讯云控制台。
2. 搜索并进入 `域名注册`。
3. 打开 `信息模板` 或 `域名信息模板`。
4. 点击 `新建模板`。
5. 使用个人身份信息填写：

```text
用户类型：个人
所有者类型：个人
域名所有者：必须与身份证姓名一致
证件类型：居民身份证
证件号码：按页面要求填写
联系人邮箱：填写长期可用的邮箱
联系人手机：填写可接收验证码的手机号
```

6. 上传或提交所需证件信息。
7. 提交实名认证审核。
8. 等待模板状态变为 `已实名认证` 或 `审核通过`。

腾讯云官方说明，信息模板一般需要 1 至 3 个工作日审核。未完成域名实名认证时，域名可能处于 `Serverhold` 状态，无法正常解析。

---

## 五、查询并购买域名

1. 在腾讯云打开 `域名注册` 页面。
2. 输入想要的域名前缀，例如 `jackye`。
3. 查询 `.com`、`.me`、`.net` 等后缀是否可注册。
4. 如果显示 `已注册`，更换前缀或后缀。
5. 如果显示 `立即购买` 或 `加入购物车`，将域名加入购物车。

选择域名时建议：

- 优先选择简短、容易拼写的 `.com`。
- 不使用连串数字、复杂连字符或容易拼错的单词。
- 不要购买明显侵犯他人商标的域名。
- 如果未来考虑大陆 ICP 备案，先确认域名后缀在工信部可备案范围内。

进入结算页面后：

```text
购买时长：建议先买 1 年
信息模板：选择上一章审核通过的个人模板
DNS 服务：使用腾讯云默认 DNSPod
自动续费：建议开启
域名隐私保护：如果页面提供，建议开启
```

确认首年价格和续费价格后支付。

支付完成后：

1. 返回 `域名注册控制台`。
2. 确认域名状态正常。
3. 确认实名认证状态为已通过。
4. 记录完整域名。本文后面用 `example.com` 代替你购买的真实域名。

从这一刻起，凡是看到 `example.com`，都要换成你的真实域名。

---

## 六、购买轻量应用服务器

1. 在腾讯云搜索 `轻量应用服务器`。
2. 进入轻量应用服务器购买页面。
3. 按照以下建议选择：

```text
地域：中国香港
可用区：随机分配或默认
应用创建方式：操作系统镜像
操作系统：Ubuntu 24.04 LTS 64位
CPU：2 核即可
内存：2 GB 即可
系统盘：40 GB 或以上
公网带宽：选择套餐默认值
月流量：个人静态网站选择入门套餐即可
数量：1 台
购买时长：先买 1 至 3 个月测试，稳定后续费
自动续费：希望长期运行时开启
```

不要选择 Windows Server。这个网站使用 Ubuntu 和 Nginx，更节省内存，也更方便维护。

对于当前只有 HTML 和图片的网站，`2 核 CPU + 2 GB 内存` 已经很充足。不要为了“性能”购买昂贵配置，访问速度更依赖服务器地域、线路、图片大小和公网带宽。

4. 核对地域确实是 `中国香港`。
5. 点击 `立即购买` 并支付。
6. 等待实例创建完成，通常需要几分钟。

---

## 七、记录服务器信息

进入 `轻量应用服务器控制台`，点击刚购买的实例，记录：

```text
实例名称：例如 JackYe-Web
公网 IPv4：例如 43.XXX.XXX.XXX
操作系统：Ubuntu 24.04 LTS
地域：中国香港
```

本文后面使用 `SERVER_IP` 表示服务器公网 IP。执行命令时不要输入 `SERVER_IP` 这几个字，而是换成真实 IP，例如：

```powershell
ssh ubuntu@43.XXX.XXX.XXX
```

### 设置登录方式

推荐使用 SSH 密钥，但第一次部署也可以先设置一个高强度密码。

简单方式：

1. 在实例详情中找到 `重置密码`。
2. 为 Ubuntu 用户或实例设置高强度密码。
3. 按控制台要求重启实例。

更安全的方式：

1. 在轻量应用服务器控制台打开 `SSH 密钥`。
2. 创建或导入密钥。
3. 将密钥绑定到同地域的实例。
4. 妥善保存私钥文件，不要上传到网站或 GitHub。

Ubuntu 镜像的默认登录用户名通常为 `ubuntu`。腾讯云官方说明，绑定密钥后，Linux 实例默认会禁止 root 密码登录，这是更安全的做法。

---

## 八、开放服务器端口

网站需要三个 TCP 端口：

| 端口 | 用途 | 来源 |
| --- | --- | --- |
| 22 | SSH 远程管理 | 初学阶段可设 `0.0.0.0/0`，稳定后建议限制为自己的 IP |
| 80 | HTTP 网站和 HTTPS 证书验证 | `0.0.0.0/0` |
| 443 | HTTPS 网站 | `0.0.0.0/0` |

在腾讯云控制台操作：

1. 打开实例详情。
2. 点击 `防火墙`。
3. 查看是否已有 Linux 登录规则，即 TCP 22。
4. 点击 `添加规则`，增加 HTTP 80。
5. 再增加 HTTPS 443。
6. 协议选择 TCP，来源填写 `0.0.0.0/0`。

不要开放数据库端口、宝塔面板端口或全部端口。当前网站只需要 22、80、443。

---

## 九、从 Windows 登录服务器

在 Windows 打开 PowerShell，输入：

```powershell
ssh ubuntu@SERVER_IP
```

示例：

```powershell
ssh ubuntu@43.XXX.XXX.XXX
```

第一次连接会显示服务器指纹，并询问：

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

先确认 IP 是自己的服务器，然后输入：

```text
yes
```

随后输入服务器密码。输入密码时屏幕不会显示星号，这是正常现象。

登录成功后，命令提示符通常类似：

```text
ubuntu@JackYe-Web:~$
```

从这里开始，除非特别写明“本地 PowerShell”，否则命令都在服务器 SSH 窗口中执行。

---

## 十、更新服务器并安装 Nginx

在服务器中执行：

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

启动 Nginx，并设置开机启动：

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

状态中看到以下内容说明成功：

```text
Active: active (running)
```

按 `q` 退出状态查看。

现在在本地浏览器访问：

```text
http://SERVER_IP
```

如果看到 Nginx 欢迎页，说明服务器、80 端口和 Nginx 都正常。

如果无法访问，依次检查：

1. 腾讯云实例是否处于运行状态。
2. 公网 IP 是否输入正确。
3. 腾讯云防火墙是否开放 TCP 80。
4. Nginx 是否为 `active (running)`。

---

## 十一、配置 Ubuntu 防火墙

腾讯云控制台防火墙是外层防火墙，Ubuntu 的 UFW 是服务器内部防火墙。建议两层都配置。

先放行 SSH，避免把自己锁在服务器外：

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

如果系统询问是否继续，输入 `y`。

正常输出应包含：

```text
OpenSSH                    ALLOW
Nginx Full                 ALLOW
```

`Nginx Full` 会放行 80 和 443。一定要先执行 `sudo ufw allow OpenSSH`，再启用 UFW。

---

## 十二、在本地打包网站文件

不要关闭 SSH 窗口。再打开一个新的 Windows PowerShell 窗口，在本地执行：

```powershell
cd "D:\Users\yxh\Desktop\yxhBlog"
```

先本地预览一次：

```powershell
python -m http.server 8080
```

浏览器打开 `http://localhost:8080/`，确认主页、我的项目、文章、个人旅行、图片、主题和语言切换正常。检查完成后按 `Ctrl+C` 停止。

删除以前可能生成的压缩包：

```powershell
Remove-Item -LiteralPath ".\site.tar.gz" -ErrorAction SilentlyContinue
```

只打包网站实际需要的文件：

```powershell
tar -czf site.tar.gz index.html assets "trip photos"
```

检查压缩包已经生成：

```powershell
Get-Item .\site.tar.gz
```

---

## 十三、把网站上传到服务器

仍然在本地 PowerShell 中执行：

```powershell
scp .\site.tar.gz ubuntu@SERVER_IP:/tmp/site.tar.gz
```

示例：

```powershell
scp .\site.tar.gz ubuntu@43.XXX.XXX.XXX:/tmp/site.tar.gz
```

输入密码并等待上传完成。

如果使用私钥：

```powershell
scp -i "C:\安全位置\your-key.pem" .\site.tar.gz ubuntu@SERVER_IP:/tmp/site.tar.gz
```

上传完成后，回到已经登录服务器的 SSH 窗口，创建网站目录：

```bash
sudo mkdir -p /var/www/jackye
sudo rm -rf /var/www/jackye/*
sudo tar -xzf /tmp/site.tar.gz -C /var/www/jackye
sudo chown -R www-data:www-data /var/www/jackye
sudo find /var/www/jackye -type d -exec chmod 755 {} \;
sudo find /var/www/jackye -type f -exec chmod 644 {} \;
```

说明：

- `/var/www/jackye` 是网站在服务器上的正式目录。
- `www-data` 是 Ubuntu 中 Nginx 使用的用户。
- 目录权限设为 755，文件权限设为 644。
- `rm -rf` 命令只用于清空明确指定的 `/var/www/jackye/*`，不要修改成 `/var/www/*` 或 `/`。

检查文件：

```bash
find /var/www/jackye -maxdepth 2 -type f | sort
```

应能看到 `index.html`、`assets/avatar.png` 和旅行照片。

---

## 十四、配置 Nginx 使用网站文件

在服务器中创建 Nginx 配置：

```bash
sudo nano /etc/nginx/sites-available/jackye
```

将下面内容粘贴进去，并把两处 `example.com` 换成真实域名：

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name example.com www.example.com;

    root /var/www/jackye;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location ~* \.(jpg|jpeg|png|gif|webp|ico)$ {
        expires 7d;
        add_header Cache-Control "public";
    }
}
```

在 nano 中保存：

1. 按 `Ctrl+O`。
2. 按回车确认文件名。
3. 按 `Ctrl+X` 退出。

启用网站配置：

```bash
sudo ln -s /etc/nginx/sites-available/jackye /etc/nginx/sites-enabled/jackye
sudo rm -f /etc/nginx/sites-enabled/default
sudo nginx -t
```

正常结果：

```text
syntax is ok
test is successful
```

然后重新加载 Nginx：

```bash
sudo systemctl reload nginx
```

再次访问：

```text
http://SERVER_IP
```

此时应该看到 Jack Ye 个人网站，不再是 Nginx 欢迎页。

---

## 十五、把域名解析到服务器

DNS 的作用是把 `example.com` 转换成服务器公网 IP。

1. 登录腾讯云控制台。
2. 搜索并进入 `云解析 DNS`。
3. 找到购买的域名，点击 `解析` 或 `解析设置`。
4. 点击 `添加记录`。

添加第一条记录：

```text
主机记录：@
记录类型：A
线路类型：默认
记录值：SERVER_IP，即服务器公网 IPv4
TTL：默认，例如 600 秒
```

添加第二条记录：

```text
主机记录：www
记录类型：A
线路类型：默认
记录值：同一个服务器公网 IPv4
TTL：默认
```

不要在记录值中填写：

```text
http://SERVER_IP
https://SERVER_IP
example.com
```

A 记录的记录值只能填写 IPv4 地址。

保存后等待 DNS 生效，通常几分钟内开始生效，也可能需要更长时间。

在本地 Windows PowerShell 检查：

```powershell
Resolve-DnsName example.com
Resolve-DnsName www.example.com
```

返回结果中的 IP 应与服务器公网 IP 一致。

也可以执行：

```powershell
nslookup example.com
nslookup www.example.com
```

DNS 生效后，浏览器访问：

```text
http://example.com
http://www.example.com
```

两者都应显示个人网站。

---

## 十六、配置免费 HTTPS

只有在以下条件全部满足后再申请证书：

- 域名 A 记录已经指向服务器公网 IP。
- `http://example.com` 可以访问网站。
- `http://www.example.com` 可以访问网站。
- 腾讯云防火墙已开放 80 和 443。
- UFW 已允许 `Nginx Full`。

在服务器中安装 Certbot：

```bash
sudo apt update
sudo apt install snapd -y
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot
sudo ln -sf /snap/bin/certbot /usr/bin/certbot
```

申请证书并让 Certbot 自动修改 Nginx：

```bash
sudo certbot --nginx -d example.com -d www.example.com
```

按提示操作：

1. 输入长期可用的邮箱。
2. 同意服务条款。
3. 是否接收新闻邮件可自行选择。
4. 如果询问是否把 HTTP 重定向到 HTTPS，选择重定向。

成功后访问：

```text
https://example.com
https://www.example.com
```

地址栏应该显示安全锁标志或安全连接信息。

测试证书自动续期：

```bash
sudo certbot renew --dry-run
```

查看 Certbot 定时任务：

```bash
systemctl list-timers | grep certbot
```

Let's Encrypt 证书有效期较短，但 Certbot 会配置自动续期。服务器必须持续运行，并保持 80 或相关验证方式可用。

---

## 十七、最终上线检查

依次检查以下地址：

```text
http://example.com
http://www.example.com
https://example.com
https://www.example.com
```

理想结果：所有地址最终都自动进入同一个 HTTPS 网站。

继续检查：

- [ ] 主页不需要滚动即可看到完整内容。
- [ ] 头像正常加载。
- [ ] 主页、我的项目、文章、个人旅行按钮正常。
- [ ] 中英文切换正常。
- [ ] 黑白主题切换正常。
- [ ] 15 张旅行照片全部正常加载。
- [ ] 手机访问没有横向滚动。
- [ ] 刷新 `https://example.com/#travel` 后页面正常。
- [ ] 浏览器显示 HTTPS 安全连接。
- [ ] 腾讯云域名和服务器都已设置到期提醒或自动续费。

---

## 十八、以后如何更新网站

每次在本地修改完成后，先预览，再重新打包和上传。

### 1. 本地重新打包

在 Windows PowerShell 执行：

```powershell
cd "D:\Users\yxh\Desktop\yxhBlog"
Remove-Item -LiteralPath ".\site.tar.gz" -ErrorAction SilentlyContinue
tar -czf site.tar.gz index.html assets "trip photos"
```

### 2. 上传新压缩包

```powershell
scp .\site.tar.gz ubuntu@SERVER_IP:/tmp/site.tar.gz
```

### 3. 在服务器替换网站

通过 SSH 登录：

```powershell
ssh ubuntu@SERVER_IP
```

然后在服务器执行：

```bash
sudo rm -rf /var/www/jackye/*
sudo tar -xzf /tmp/site.tar.gz -C /var/www/jackye
sudo chown -R www-data:www-data /var/www/jackye
sudo find /var/www/jackye -type d -exec chmod 755 {} \;
sudo find /var/www/jackye -type f -exec chmod 644 {} \;
sudo nginx -t
sudo systemctl reload nginx
```

最后使用浏览器无痕窗口打开网站，或按 `Ctrl+F5` 强制刷新。

---

## 十九、基础安全维护

### 每月更新一次系统

```bash
sudo apt update
sudo apt upgrade -y
sudo systemctl status nginx
sudo certbot renew --dry-run
```

### 使用 SSH 密钥后关闭密码登录

先确认密钥登录完全可用，再考虑关闭密码登录。不要在没有验证密钥的情况下直接关闭密码，否则可能无法再次登录服务器。

### 不安装不需要的软件

当前静态网站不需要：

- MySQL
- PHP
- Node.js
- Docker
- 宝塔面板
- FTP 服务

软件越少，攻击面和维护成本越低。

### 定期备份

本地 `yxhBlog` 文件夹应始终保留。还可以将项目保存到私有或公开 Git 仓库。腾讯云控制台中可以为服务器创建快照，但网站源文件的本地副本仍然最重要。

### 保护个人信息

网站目前公开展示出生日期、地区和邮箱。上线前确认这些信息确实希望长期公开，因为搜索引擎和网页存档服务可能保存它们。

---

## 二十、常见故障排查

### 域名完全打不开

检查 DNS：

```powershell
Resolve-DnsName example.com
```

如果没有返回服务器 IP，问题在域名解析。检查 A 记录、域名实名认证和 DNS 是否生效。

### IP 能访问，域名不能访问

检查：

1. DNS A 记录是否指向正确 IP。
2. Nginx `server_name` 是否填写真实域名。
3. 修改 Nginx 后是否执行 `sudo nginx -t` 和 `sudo systemctl reload nginx`。

### 出现 Nginx 默认欢迎页

检查默认站点是否已删除：

```bash
ls -la /etc/nginx/sites-enabled/
```

应看到 `jackye`，不应看到 `default`。必要时执行：

```bash
sudo rm -f /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```

### 出现 403 Forbidden

检查目录和权限：

```bash
ls -la /var/www/jackye
sudo chown -R www-data:www-data /var/www/jackye
sudo find /var/www/jackye -type d -exec chmod 755 {} \;
sudo find /var/www/jackye -type f -exec chmod 644 {} \;
```

确认文件名严格为小写 `index.html`。

### 图片显示 404

检查服务器文件：

```bash
find /var/www/jackye -maxdepth 2 -type f | sort
```

Linux 区分大小写，例如 `Tokyo1.jpg` 和 `tokyo1.jpg` 是两个不同文件。`trip photos` 文件夹中的空格本身没有问题，浏览器会将它编码为 `%20`。

### SSH 无法连接

检查：

1. 服务器是否运行。
2. IP 是否正确。
3. 腾讯云防火墙是否开放 22。
4. UFW 是否允许 OpenSSH。
5. 用户名是否为 `ubuntu`。
6. 密码或私钥是否正确。

### HTTPS 证书申请失败

在服务器执行：

```bash
sudo nginx -t
sudo systemctl status nginx
```

在本地执行：

```powershell
Resolve-DnsName example.com
Resolve-DnsName www.example.com
```

确保两个域名都指向当前服务器，并且 HTTP 可以正常访问。域名刚修改解析时，等待 DNS 生效后再重试 Certbot。

### 修改后网站还是旧内容

1. 确认新压缩包确实上传完成。
2. 检查服务器文件时间：

```bash
ls -l /var/www/jackye/index.html
```

3. 强制刷新浏览器 `Ctrl+F5`。
4. 使用无痕窗口测试。
5. 图片配置了 7 天缓存；替换图片但文件名不变时，浏览器可能继续使用旧图。可以给图片换文件名，或暂时清除缓存。

---

## 二十一、如果选择中国大陆服务器

如果购买上海、北京、广州等中国大陆地域服务器，整体技术部署步骤仍然相同，但在开放网站访问前需要增加 ICP 备案流程。

基本顺序：

1. 完成腾讯云账号实名认证。
2. 购买可用于备案的中国大陆云服务器或轻量应用服务器。
3. 服务器通常需要包年包月购买至少 3 个月，并满足腾讯云当时的备案资源要求。
4. 域名完成实名认证，域名所有者信息与备案主体保持一致。
5. 在腾讯云备案控制台或备案小程序发起首次备案。
6. 填写主办者、网站名称、网站用途、负责人和服务器信息。
7. 完成人脸核验、短信核验及平台初审。
8. 等待所在地通信管理局审核。
9. 获得 ICP 备案号后，将备案号按要求展示在网站底部并链接至工信部备案系统。
10. 根据当地要求完成公安联网备案。
11. 再正式开放域名访问。

腾讯云明确说明：中国大陆服务器开办网站必须先取得 ICP 备案号；中国香港及境外服务器无需用于 ICP 备案，也不能作为大陆备案资源。备案政策会更新，实际办理时必须以腾讯云备案系统和当地通信管理局的最新要求为准。

---

## 二十二、官方参考资料

- [腾讯云：单个域名注册](https://cloud.tencent.com/document/product/242/9595)
- [腾讯云：域名实名认证](https://cloud.tencent.com/document/product/242/6707)
- [腾讯云：轻量应用服务器购买方式](https://cloud.tencent.com/document/product/1207/44580)
- [腾讯云：轻量应用服务器 SSH 密钥](https://cloud.tencent.com/document/product/1207/44573)
- [腾讯云：云解析 DNS](https://cloud.tencent.com/document/product/302)
- [腾讯云：ICP备案快速入门](https://cloud.tencent.com/document/product/243/39038)
- [腾讯云：备案云资源要求](https://cloud.tencent.com/document/product/243/18908)
- [Ubuntu：UFW 防火墙](https://ubuntu.com/server/docs/security-firewall/)
- [Certbot 官方文档](https://eff-certbot.readthedocs.io/)
