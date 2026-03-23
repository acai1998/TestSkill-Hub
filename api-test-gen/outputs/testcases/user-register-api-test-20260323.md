## 一、接口概览
| 接口名称 | 路径 | 方法 | 鉴权要求 | 说明 |
| --- | --- | --- | --- | --- |
| 发送注册验证码 | `/api/sms/send` | POST | 无 | 向手机号发送注册验证码 |
| 用户注册 | `/api/user/register` | POST | 无 | 手机号+验证码+密码完成注册 |
| 获取用户信息 | `/api/user/profile` | GET | Bearer Token | 获取当前登录用户信息 |

---

## 二、测试用例

### 接口一：发送注册验证码 [POST /api/sms/send]

**接口定义：**
```json
Request Body:
{
  "phone": "string, 必填, 11位手机号",
  "type": "string, 必填, 枚举值: register/login/reset"
}

Response 200:
{
  "code": 0,
  "msg": "发送成功",
  "data": { "expireIn": 60 }
}
```

#### 正常路径
| 用例编号 | 场景描述 | 请求参数 | 预期状态码 | 预期响应 |
| --- | --- | --- | --- | --- |
| SMS-001 | 合法手机号发送注册验证码 | `{"phone":"13812345678","type":"register"}` | 200 | `code=0, msg="发送成功", data.expireIn=60` |

#### 异常路径
| 用例编号 | 场景描述 | 异常点 | 请求参数 | 预期状态码 | 预期响应 |
| --- | --- | --- | --- | --- | --- |
| SMS-002 | 缺少 phone 参数 | 必填参数缺失 | `{"type":"register"}` | 400 | `code≠0, msg含"phone"或"手机号"` |
| SMS-003 | 缺少 type 参数 | 必填参数缺失 | `{"phone":"13812345678"}` | 400 | `code≠0, msg含"type"` |
| SMS-004 | 手机号少于11位 | 边界值 | `{"phone":"1381234567","type":"register"}` | 400 | `code≠0, msg含"手机号格式"` |
| SMS-005 | 手机号多于11位 | 边界值 | `{"phone":"138123456789","type":"register"}` | 400 | `code≠0, msg含"手机号格式"` |
| SMS-006 | 手机号含字母 | 格式错误 | `{"phone":"1381234567a","type":"register"}` | 400 | `code≠0, msg含"手机号格式"` |
| SMS-007 | type 传入非法枚举值 | 枚举非法 | `{"phone":"13812345678","type":"unknown"}` | 400 | `code≠0, msg含"type"或"参数"` |
| SMS-008 | 60s内重复发送 | 频率限制 | 同 SMS-001，60s内第二次调用 | 429 或 200 含业务码 | `code≠0, msg含"请稍后再试"或"频繁"` |
| SMS-009 | 已注册手机号发送注册类验证码 | 业务逻辑 | `{"phone":"已注册手机号","type":"register"}` | 200 或 400 | `code≠0, msg含"已注册"` |
| SMS-010 | phone 传空字符串 | 边界值 | `{"phone":"","type":"register"}` | 400 | `code≠0, msg含"手机号"` |

#### 鉴权用例
| 用例编号 | 场景描述 | 鉴权条件 | 预期状态码 |
| --- | --- | --- | --- |
| SMS-A001 | 此接口无需鉴权，正常调用 | 不传 Authorization Header | 200（正常处理） |

---

### 接口二：用户注册 [POST /api/user/register]

**接口定义：**
```json
Request Body:
{
  "phone": "string, 必填, 11位手机号",
  "code": "string, 必填, 6位验证码",
  "password": "string, 必填, 6-20位"
}

Response 200:
{
  "code": 0,
  "msg": "注册成功",
  "data": {
    "userId": "string",
    "token": "string"
  }
}
```

#### 正常路径
| 用例编号 | 场景描述 | 请求参数 | 预期状态码 | 预期响应 |
| --- | --- | --- | --- | --- |
| REG-001 | 所有参数合法，注册成功 | `{"phone":"13812345678","code":"123456","password":"Test1234"}` | 200 | `code=0, data.userId非空, data.token非空` |

#### 异常路径
| 用例编号 | 场景描述 | 异常点 | 请求参数 | 预期状态码 | 预期响应 |
| --- | --- | --- | --- | --- | --- |
| REG-002 | 缺少 phone | 必填参数缺失 | `{"code":"123456","password":"Test1234"}` | 400 | `code≠0, msg含"phone"` |
| REG-003 | 缺少 code | 必填参数缺失 | `{"phone":"13812345678","password":"Test1234"}` | 400 | `code≠0, msg含"code"或"验证码"` |
| REG-004 | 缺少 password | 必填参数缺失 | `{"phone":"13812345678","code":"123456"}` | 400 | `code≠0, msg含"password"或"密码"` |
| REG-005 | 全部参数缺失 | 必填参数缺失 | `{}` | 400 | `code≠0` |
| REG-006 | 验证码错误 | 业务逻辑 | `{"phone":"13812345678","code":"000000","password":"Test1234"}` | 200 或 400 | `code≠0, msg含"验证码错误"` |
| REG-007 | 验证码过期 | 业务逻辑 | 使用60s前的验证码 | 200 或 400 | `code≠0, msg含"验证码已过期"` |
| REG-008 | 密码5位（边界-1） | 边界值 | `{"phone":"13812345678","code":"123456","password":"Ab123"}` | 400 | `code≠0, msg含"密码长度"` |
| REG-009 | 密码6位（边界最小值） | 边界值 | `{"phone":"13812345678","code":"123456","password":"Ab1234"}` | 200 | `code=0, 注册成功` |
| REG-010 | 密码20位（边界最大值） | 边界值 | `{"phone":"13812345678","code":"123456","password":"Abcdefghij1234567890"}` | 200 | `code=0, 注册成功` |
| REG-011 | 密码21位（边界+1） | 边界值 | `{"phone":"13812345678","code":"123456","password":"Abcdefghij12345678901"}` | 400 | `code≠0, msg含"密码长度"` |
| REG-012 | 密码为空字符串 | 边界值 | `{"phone":"13812345678","code":"123456","password":""}` | 400 | `code≠0, msg含"密码"` |
| REG-013 | 手机号已注册 | 业务逻辑 | 使用已注册的手机号 | 200 或 400 | `code≠0, msg含"已注册"` |
| REG-014 | code 字段传整型 | 类型错误 | `{"phone":"13812345678","code":123456,"password":"Test1234"}` | 400 或 200 | 若200则需校验业务逻辑正确；若400则 msg 含类型错误信息 |
| REG-015 | password 传数组 | 类型错误 | `{"phone":"13812345678","code":"123456","password":["Test1234"]}` | 400 | `code≠0` |

#### 鉴权用例
| 用例编号 | 场景描述 | 鉴权条件 | 预期状态码 |
| --- | --- | --- | --- |
| REG-A001 | 此接口无需鉴权，正常调用 | 不传 Authorization Header | 200（正常处理） |
| REG-A002 | 已登录用户调用注册接口 | 传入有效 Token | 200 或 400 | 需确认是否允许已登录状态注册 |

---

### 接口三：获取用户信息 [GET /api/user/profile]

**接口定义：**
```
Headers: Authorization: Bearer <token>

Response 200:
{
  "code": 0,
  "data": {
    "userId": "string",
    "phone": "string",
    "nickname": "string",
    "createdAt": "string"
  }
}
```

#### 正常路径
| 用例编号 | 场景描述 | 请求参数 | 预期状态码 | 预期响应 |
| --- | --- | --- | --- | --- |
| PROFILE-001 | 有效 Token 获取用户信息 | `Authorization: Bearer <valid_token>` | 200 | `code=0, data.userId非空, data.phone为脱敏手机号` |

#### 异常路径
| 用例编号 | 场景描述 | 异常点 | 请求参数 | 预期状态码 | 预期响应 |
| --- | --- | --- | --- | --- | --- |
| PROFILE-002 | 传入其他用户 Token | 水平越权 | 使用用户B的Token查询 | 200 | `data.userId` 为 Token 对应用户，不能返回其他用户信息 |

#### 鉴权用例
| 用例编号 | 场景描述 | 鉴权条件 | 预期状态码 |
| --- | --- | --- | --- |
| PROFILE-A001 | 无 Token | 不传 Authorization Header | 401 |
| PROFILE-A002 | Token 格式非法 | `Authorization: Bearer invalid_abc` | 401 |
| PROFILE-A003 | Token 已过期 | 使用过期 Token | 401 |
| PROFILE-A004 | 有效 Token | 正常 Token | 200 |

---

## 三、测试覆盖矩阵
| 接口 | 正常路径 | 参数异常 | 鉴权 | 边界值 | 幂等性 |
| --- | --- | --- | --- | --- | --- |
| POST /api/sms/send | ✅ SMS-001 | ✅ SMS-002~010 | ✅ SMS-A001 | ✅ SMS-004,005,010 | N/A |
| POST /api/user/register | ✅ REG-001 | ✅ REG-002~015 | ✅ REG-A001,A002 | ✅ REG-008~012 | N/A |
| GET /api/user/profile | ✅ PROFILE-001 | ✅ PROFILE-002 | ✅ PROFILE-A001~A004 | N/A | N/A |

## 四、待确认项
- [ ] `POST /api/sms/send` 60s内重复发送是否返回429，还是200含业务错误码
- [ ] `POST /api/user/register` 验证码错误返回 400 还是 200+业务码（需统一约定）
- [ ] `GET /api/user/profile` 返回的 phone 字段是否脱敏（如 138****5678）
- [ ] 已登录用户访问注册接口的预期行为是否有明确定义
