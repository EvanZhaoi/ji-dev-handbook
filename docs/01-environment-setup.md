# 01. 环境搭建

## 基础要求

建议在 Windows 上做最终测试，在 macOS 上也可以开发大部分前端和后端功能。

必备环境：

- Git
- Node.js，建议通过 `n` 管理版本
- Java 21，建议通过 `jenv` 管理版本
- Rust stable
- MySQL 8.x
- pnpm 或 npm
- VS Code / IntelliJ IDEA

## Node 环境

使用 `n`：

```bash
npm install -g n
sudo n lts
node -v
npm -v
```

建议项目根目录固定 Node 版本：

```bash
node -v > .node-version
```

## Java 环境

使用 `jenv`：

```bash
brew install jenv
jenv add /Library/Java/JavaVirtualMachines/<jdk-21>/Contents/Home
jenv global 21
java -version
```

项目内固定版本：

```bash
jenv local 21
```

## Rust 和 Tauri

```bash
rustup update
rustc --version
cargo --version
```

安装 Tauri 依赖：

```bash
npm create tauri-app@latest
```

Windows 需要额外安装 Microsoft C++ Build Tools 和 WebView2 Runtime。

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

```bash
git --version
node -v
npm -v
java -version
rustc --version
cargo --version
mysql --version
```

