# 02. 仓库结构

建议拆成三个仓库，和当前应用保持一致：

```text
ji-planning      # 需求、设计、开发过程记录
ji-desktop       # Tauri + React 桌面端
ji-server        # Spring Boot 后端
```

如果你只是一个人从零学习，也可以先放在一个总目录里：

```text
ji/
├── ji-desktop/
├── ji-server/
└── ji-dev-handbook/
```

## ji-desktop 推荐结构

```text
src/
├── components/
│   ├── layout/
│   └── ui/
├── core/
│   ├── http/
│   └── query/
├── desktop/
│   ├── device/
│   ├── notification/
│   ├── shortcut/
│   └── user/
├── features/
│   ├── auth/
│   ├── memo/
│   ├── notify/
│   └── settings/
├── hooks/
├── i18n/
├── services/
├── stores/
├── styles/
└── utils/
```

规则：

- `components/ui` 只放无业务基础组件。
- `features/*` 按业务拆模块。
- `desktop/*` 封装 Tauri 能力。
- 组件不直接拼接口 URL，统一走 `features/*/api`。
- 服务端数据用 TanStack Query，界面状态用 Zustand。

## ji-server 推荐结构

```text
src/main/java/com/ji/
├── common/
├── config/
├── module/
│   ├── auth/
│   ├── member/
│   ├── memo/
│   └── notify/
└── JiServerApplication.java
```

规则：

- `common` 放统一返回、异常、分页、错误码。
- `config` 只放框架配置。
- `module/*` 按业务拆分。
- 每个业务模块内部再拆 `controller / dto / entity / mapper / service`。
- 简单 CRUD 用 MyBatis-Plus，复杂列表查询保留显式 SQL。

