# 08. 数据库与 API

## 数据库原则

- SQL 文件只放 `CREATE DATABASE` 和 `CREATE TABLE`。
- 不在初始化 SQL 里写迁移更新语句。
- 所有表都加 `COMMENT`。
- 用户主数据不放在本系统，系统只保存快照。
- 权限以 token 中的 username 判断，不信任前端传入 owner。

## Memo 表

建议表：

- `ji_memo_group`
- `ji_memo`
- `ji_memo_related_user`
- `ji_memo_top`
- `ji_memo_favorite`

## 认证表

建议表：

- `ji_user_snapshot`
- `ji_auth_audit`
- `ji_device_trust`
- `ji_device_verification`

## 通知表

建议表：

- `ji_notification`
- `ji_notification_recipient`

## API 风格

统一响应：

```json
{
  "code": 200,
  "message": "success",
  "data": {}
}
```

分页响应：

```json
{
  "list": [],
  "total": 100,
  "page": 1,
  "size": 50,
  "pages": 2
}
```

## API 分组

```text
/api/v1/auth/**
/api/v1/members/**
/api/v1/memos/**
/api/v1/memo-groups/**
/api/v1/notifications/**
```

