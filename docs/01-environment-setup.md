# 01. 环境搭建

## 基础要求

本手册默认你在 Windows 电脑上开发和测试。后续所有命令优先使用 PowerShell，桌面能力也以 Windows 托盘、Windows 通知中心、Windows 全局快捷键为准。

必备环境：

- Git
- Node.js，建议通过 `nvm-windows` 管理版本
- Java 21，建议通过 Scoop 或 jabba 管理版本
- Rust stable
- MySQL 8.x
- pnpm
- VS Code / IntelliJ IDEA
- Microsoft C++ Build Tools
- Microsoft Edge WebView2 Runtime

## Node 环境

使用 `nvm-windows`：

```powershell
nvm install 22
nvm use 22
node -v
corepack enable
corepack prepare pnpm@latest --activate
pnpm -v
```

建议项目根目录固定 Node 版本：

```powershell
node -v > .node-version
```

## Java 环境

推荐使用 Scoop 安装 Temurin JDK 21：

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
iwr -useb get.scoop.sh | iex
scoop bucket add java
scoop install temurin21-jdk
java -version
```

如果你更喜欢专门的 Java 版本管理器，也可以使用 jabba：

```powershell
jabba install temurin@1.21.0
jabba use temurin@1.21.0
java -version
```

## Rust 和 Tauri

```powershell
rustup update
rustc --version
cargo --version
```

安装 Tauri 依赖：

```powershell
pnpm create tauri-app@latest
```

Windows 需要额外安装：

- Microsoft C++ Build Tools
- Windows 10/11 SDK
- Microsoft Edge WebView2 Runtime

安装完成后重启 PowerShell，再运行：

```powershell
rustc --version
cargo --version
```

## MySQL

建议本地创建数据库：

```sql
CREATE DATABASE IF NOT EXISTS ji
  DEFAULT CHARACTER SET utf8mb4
  DEFAULT COLLATE utf8mb4_0900_ai_ci;
```

开发配置建议放在后端 `application.yml`：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ji?useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: your_password
```

## 环境验证清单

```powershell
git --version
node -v
pnpm -v
java -version
rustc --version
cargo --version
mysql --version
```

## 推荐 Windows 目录

建议统一放在：

```text
C:\Documents\VSCodeProjects\
```

例如：

```text
C:\Documents\VSCodeProjects\ji-desktop
C:\Documents\VSCodeProjects\ji-server
C:\Documents\VSCodeProjects\ji-dev-handbook
```
