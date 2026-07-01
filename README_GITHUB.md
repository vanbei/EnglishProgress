# GitHub Actions 自动构建 APK 指南

## 步骤一：注册 GitHub 账号
1. 打开 https://github.com
2. 点击 Sign up，用邮箱注册（免费）

## 步骤二：创建仓库
1. 登录后点击右上角 + 号 → New repository
2. 仓库名填：EnglishProgress
3. 选择 Public（公开，免费）
4. 点击 Create repository

## 步骤三：上传代码
在仓库页面，点击 "uploading an existing file"，然后把本文件夹（EnglishProgress）里的所有文件拖进去上传。

**注意**：node_modules 和 android/app/build 不需要上传（已在 .gitignore 中排除）

或者使用命令行：
```bash
cd EnglishProgress
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin https://github.com/你的用户名/EnglishProgress.git
git push -u origin main
```

## 步骤四：触发构建
代码上传后，GitHub Actions 会自动开始构建 APK。

你也可以手动触发：
1. 进入仓库 → Actions 标签页
2. 点击左侧 "Build APK"
3. 点击右侧 "Run workflow" → "Run workflow"

## 步骤五：下载 APK
1. 进入仓库 → Actions 标签页
2. 点击最新的构建记录
3. 页面底部找到 "Artifacts" 区域
4. 点击 "app-debug-apk" 下载

下载的是一个 zip 文件，解压后就是 `app-debug.apk`，可以直接安装到安卓手机。

## 后端地址
APK 已配置连接你的腾讯云服务器：
- API: http://62.234.180.189:3000/api
- 管理员账号: admin / admin123
