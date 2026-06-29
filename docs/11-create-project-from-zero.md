# 11. 从零创建项目

这一章假设你在 Windows 电脑上开发，工作目录为：

```text
C:\Documents\VSCodeProjects
```

最终得到三个目录：

```text
C:\Documents\VSCodeProjects\ji-desktop
C:\Documents\VSCodeProjects\ji-server
C:\Documents\VSCodeProjects\ji-dev-handbook
```

## 1. 创建工作目录

打开 PowerShell：

```powershell
mkdir C:\Documents\VSCodeProjects
cd C:\Documents\VSCodeProjects
```

确认环境：

```powershell
git --version
node -v
pnpm -v
java -version
rustc --version
cargo --version
mysql --version
```

## 2. 创建桌面端项目

```powershell
pnpm create tauri-app@latest ji-desktop
cd ji-desktop
pnpm install
```

推荐选择：

```text
Package manager: pnpm
UI template: React
Language: TypeScript
```

首次启动：

```powershell
pnpm tauri dev
```

如果 Tauri 可以打开一个空窗口，说明桌面端基础环境正常。

## 3. 安装桌面端依赖

```powershell
pnpm add axios zustand @tanstack/react-query @tanstack/react-virtual
pnpm add lucide-react clsx tailwind-merge class-variance-authority
pnpm add @radix-ui/react-select @radix-ui/react-popover @radix-ui/react-tabs
pnpm add @radix-ui/react-context-menu @radix-ui/react-alert-dialog @radix-ui/react-tooltip
pnpm add @milkdown/crepe @milkdown/plugin-diagram @milkdown/utils mermaid
```

安装 Tailwind：

```powershell
pnpm add -D tailwindcss postcss autoprefixer
pnpm exec tailwindcss init -p
```

建议先提交一次：

```powershell
git init
git add -A
git commit -m "chore: initialize ji desktop"
```

## 4. 创建后端项目

可以使用 Spring Initializr，也可以手动创建 Maven 项目。建议使用 Spring Initializr 生成：

```text
Project: Maven
Language: Java
Spring Boot: 当前稳定版
Group: com.ji
Artifact: ji-server
Java: 21
```

依赖先选择：

- Spring Web
- Spring WebSocket
- Validation
- JDBC API
- MySQL Driver
- Lombok

下载后解压到：

```text
C:\Documents\VSCodeProjects\ji-server
```

进入目录：

```powershell
cd C:\Documents\VSCodeProjects\ji-server
mvn test
```

## 5. 增加后端核心依赖

在 `pom.xml` 中增加：

```xml
<properties>
    <java.version>21</java.version>
    <sa-token.version>1.45.0</sa-token.version>
    <mybatis-plus.version>3.5.16</mybatis-plus.version>
    <springdoc.version>3.0.3</springdoc.version>
</properties>
```

增加依赖：

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-spring-boot3-starter</artifactId>
    <version>${mybatis-plus.version}</version>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-jsqlparser</artifactId>
    <version>${mybatis-plus.version}</version>
</dependency>
<dependency>
    <groupId>cn.dev33</groupId>
    <artifactId>sa-token-spring-boot3-starter</artifactId>
    <version>${sa-token.version}</version>
</dependency>
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>${springdoc.version}</version>
</dependency>
```

验证：

```powershell
mvn test
```

## 6. 创建数据库

登录 MySQL：

```powershell
mysql -uroot -p
```

执行：

```sql
CREATE DATABASE IF NOT EXISTS ji
  DEFAULT CHARACTER SET utf8mb4
  DEFAULT COLLATE utf8mb4_0900_ai_ci;
```

后端 `src/main/resources/application.yml`：

```yaml
server:
  port: 28080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/ji?useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: your_password
```

## 7. 建立第一版目录

桌面端：

```powershell
mkdir src\components\ui
mkdir src\components\layout
mkdir src\core\http
mkdir src\core\query
mkdir src\features\auth
mkdir src\features\memo
mkdir src\features\notify
mkdir src\features\settings
mkdir src\desktop\notification
mkdir src\desktop\shortcut
mkdir src\desktop\user
mkdir src\stores
mkdir src\styles
```

后端：

```powershell
mkdir src\main\java\com\ji\common
mkdir src\main\java\com\ji\config
mkdir src\main\java\com\ji\module\auth
mkdir src\main\java\com\ji\module\member
mkdir src\main\java\com\ji\module\memo
mkdir src\main\java\com\ji\module\notify
mkdir src\main\resources\db
```

## 8. 第一轮验收

桌面端：

```powershell
cd C:\Documents\VSCodeProjects\ji-desktop
pnpm build
```

后端：

```powershell
cd C:\Documents\VSCodeProjects\ji-server
mvn test
```

验收标准：

- 桌面端能打开空 Tauri 窗口。
- 前端 build 通过。
- 后端 test 通过。
- MySQL 中能看到 `ji` 数据库。
- 两个项目都已经完成第一次 Git commit。

