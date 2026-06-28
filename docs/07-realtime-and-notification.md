# 07. 实时同步与通知

## WebSocket 原则

WebSocket 只推“发生了变化”，不直接推完整业务对象。

原因：

- 消息体小。
- 权限更清楚。
- 前端最终数据仍以 HTTP 查询为准。

Memo 事件示例：

```json
{
  "module": "memo",
  "type": "memo.updated",
  "ownerUsername": "evan.zhao",
  "recipientUsernames": ["evan.zhao", "leader.wang"],
  "memoId": 1,
  "memoTitle": "项目计划",
  "actorDisplayName": "Evan Zhao",
  "contentPreview": "更新了下一步计划"
}
```

## 通知规则

- 自己操作：只刷新数据，不生成通知。
- 他人操作：刷新数据，同时进入通知中心并弹系统通知。
- 分组变化：只刷新分组，不生成通知。
- 置顶/收藏：个人状态，不广播。

## 通用通知平台

通用通知用于其他系统向桌面端发送通知。

后端接口：

- 创建通知
- 查询当前用户通知
- 标记已读
- 全部已读

数据库：

- `notification`
- `notification_recipient`

实时事件：

```json
{
  "module": "notify",
  "type": "notification.created",
  "recipientUsernames": ["evan.zhao"],
  "notificationId": 1001,
  "title": "审批已更新",
  "body": "你有一条新的审批任务",
  "level": "info"
}
```

## 系统通知

桌面端使用 Tauri notification 插件。

要求：

- Windows 右下角弹出。
- 进入 Windows 通知栏。
- 点击通知可以打开主窗口。
- 如果有 memoId，跳转到对应 Memo。

