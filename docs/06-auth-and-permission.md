# 06. 认证与权限

## 目标

桌面端不做传统登录页。应用启动后识别当前 Windows 用户和设备信息，后端判断是否允许进入。

## 认证流程

```text
桌面端启动
→ 获取 Windows username / domain / computerName / deviceId
→ POST /auth/desktop/verify
→ 后端校验人员和设备
→ 签发 token
→ 前端保存 session
```

## 设备信任

逻辑：

- 用户从未绑定设备：直接绑定当前设备。
- 用户已绑定设备且当前设备一致：直接通过。
- 用户已绑定设备但当前设备不一致：发送验证码。

测试阶段：

- 验证码写后端日志。

生产阶段：

- 发送邮件到员工邮箱。

## 人员来源

先定义接口：

```java
public interface PersonnelProvider {
    EmployeeInfo findByUsername(String username);
    List<EmployeeInfo> searchMembers(String keyword, int limit);
}
```

测试阶段实现 mock。

生产阶段替换成：

- 企业人员 API
- LDAP
- AD
- HR 系统接口

业务代码只依赖 `PersonnelProvider`，不要直接依赖某个真实系统。

## Token

后续请求携带：

```http
unicomm-token: {accessToken}
```

WebSocket 因为浏览器不能稳定设置自定义 header，可以用查询参数：

```text
ws://localhost:28080/ws?token=xxx
```

