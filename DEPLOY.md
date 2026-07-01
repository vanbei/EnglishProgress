# 英语渐进 - 部署与打包指南

## 一、腾讯云服务器部署后端

### 1. 服务器准备
- 腾讯云轻量应用服务器（建议 Ubuntu 22.04 或 CentOS 8）
- 开放防火墙端口：3000（或你希望使用的端口）
- 服务器需要安装 Node.js（建议 v18+）

### 2. 安装 Node.js（Ubuntu/Debian）
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 3. 上传后端代码
把 `server/` 目录打包上传到服务器：
```bash
# 在本地打包
zip -r server.zip server/

# 上传到服务器（用 scp 或 sftp）
scp server.zip root@你的服务器IP:/opt/

# 在服务器上解压
ssh root@你的服务器IP
mkdir -p /opt/english-progress
cd /opt/english-progress
unzip /opt/server.zip
```

### 4. 配置 systemd 服务
```bash
cp /opt/english-progress/server/english-progress.service /etc/systemd/system/
systemctl daemon-reload
systemctl enable english-progress
systemctl start english-progress
systemctl status english-progress
```

### 5. 验证后端运行
```bash
curl http://你的服务器IP:3000/api/auth/login \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin123"}'
```

### 6. 配置防火墙
```bash
# Ubuntu UFW
ufw allow 3000/tcp

# 或腾讯云控制台安全组，入站规则添加 TCP 3000
```

### 7. 可选：使用 Nginx 反向代理 + HTTPS
如果你想用域名和 HTTPS，配置 Nginx：
```nginx
server {
    listen 80;
    server_name your-domain.com;
    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

## 二、打包 Android APK

### 1. 修改前端 API 地址
打开 `index.html`，找到这一行：
```javascript
const API_BASE = 'http://localhost:3000/api';
```
改成你的服务器地址（已配置好）：
```javascript
const API_BASE = 'http://62.234.180.189:3000/api';
// 如果用域名和 Nginx：
// const API_BASE = 'https://your-domain.com/api';
```

### 2. 同步到 Capacitor
```bash
copy index.html www\index.html
npx cap sync android
```

### 3. 安装 Android Studio（如果还没有）
- 下载地址：https://developer.android.com/studio
- 安装时勾选 Android SDK、Android SDK Platform、Android Virtual Device

### 4. 用 Android Studio 打开项目
```bash
npx cap open android
```

### 5. 构建 APK
在 Android Studio 中：
- 菜单栏 → Build → Build Bundle(s) / APK(s) → Build APK(s)
- 生成的 APK 在：`android/app/build/outputs/apk/debug/app-debug.apk`

### 6. 签名发布版 APK（可选）
如果要发布到应用商店，需要签名：
- Build → Generate Signed Bundle / APK
- 创建或选择密钥库（keystore）

---

## 三、局域网测试（不部署到服务器）

如果你只想在局域网内测试 APK：

1. 电脑和手机连同一个 WiFi
2. 查看电脑内网 IP：`ipconfig`（Windows）或 `ifconfig`（Mac/Linux）
3. 修改 `API_BASE` 为 `http://电脑内网IP:3000/api`
4. 启动后端：`node server/server.js`
5. 打包 APK 并安装到手机

---

## 四、文件说明

| 文件/目录 | 说明 |
|-----------|------|
| `index.html` | 前端主页面 |
| `www/` | Capacitor 打包用的 Web 资源目录 |
| `android/` | Capacitor 生成的 Android 原生项目 |
| `server/` | 纯 Node.js 后端（无 npm 依赖） |
| `server/english-progress.service` | systemd 服务配置文件 |
| `capacitor.config.json` | Capacitor 配置 |
