# 09. 测试与发布

## 每次提交前

前端：

```powershell
npm run build
git diff --check
```

后端：

```powershell
mvn test
git diff --check
```

数据库：

```powershell
mysql -uroot -p < src/main/resources/db/schema-mysql.sql
```

## 需要手动测试的桌面能力

- 托盘图标是否出现
- 关闭窗口是否进入后台
- 点击托盘是否能恢复窗口
- 全局快捷键是否能唤出主界面
- 快速 Memo 窗口是否能新增 Memo
- 系统通知是否出现在 Windows 右下角
- 系统通知是否进入 Windows 通知中心
- 点击通知是否能打开指定 Memo
- Windows 深色/浅色主题切换后应用是否跟随
- Windows 显示缩放 150% / 200% 下布局是否正常

## Memo 回归测试

- 新建 Memo 后排在正确位置
- 保存后左下角显示已保存
- 切换到其他 Memo 再回来不应显示未保存
- 长内容可以在编辑器内部滚动
- 添加相关人后相关人可见
- 只读相关人不能编辑
- 可编辑相关人可以修改标题/内容/状态
- 收藏和置顶只影响当前用户

## 发布前检查

- 数据库 SQL 可从空库创建全部表
- Windows 测试机可启动
- 无原生标题栏重复
- 字体文件已打包
- 浅色/深色/跟随系统正常
- 通知权限正常
- 快捷键冲突有提示
- 安装包在未安装开发环境的 Windows 电脑上可运行
