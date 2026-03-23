# 鉴权测试规范

## 常见鉴权方式

### Bearer Token（JWT）
| 测试场景 | 操作 | 预期结果 |
| --- | --- | --- |
| 无 Token | 不传 Authorization Header | 401 Unauthorized |
| Token 格式非法 | `Authorization: Bearer invalid_token` | 401 Unauthorized |
| Token 已过期 | 使用过期 Token | 401 Unauthorized |
| Token 有效 | 正常 Token | 200/业务成功码 |

### Cookie 鉴权
| 测试场景 | 操作 | 预期结果 |
| --- | --- | --- |
| 无 Cookie | 清除所有 Cookie | 401/302 跳转登录 |
| Cookie 篡改 | 修改 session 值 | 401 Unauthorized |
| Cookie 过期 | 使用过期 Cookie | 401/302 跳转登录 |

### API Key
| 测试场景 | 操作 | 预期结果 |
| --- | --- | --- |
| 无 API Key | 不传 key 参数 | 401/403 |
| 非法 API Key | 传入随机字符串 | 401/403 |
| 有效 API Key | 正常 key | 200/业务成功码 |

## 权限越权测试

### 水平越权（同级用户）
- 用户 A 尝试访问/修改用户 B 的资源
- 预期：403 Forbidden 或业务层拒绝

### 垂直越权（跨角色）
- 普通用户调用管理员接口
- 商家账号调用平台管理接口
- 预期：403 Forbidden

## 注意事项
1. 鉴权测试用例应与业务用例分开列表，便于单独执行。
2. 越权测试需准备至少两个不同身份的测试账号。
3. 敏感接口（涉及资金、个人信息）必须包含越权测试。
