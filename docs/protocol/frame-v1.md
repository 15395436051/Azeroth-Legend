# Azeroth-Legend Gateway Protocol v1（握手阶段）

> Draft / development-only。此协议仅用于本地连通性测试，未经过安全审查，不得承载真实账号密码或公网认证。

## 1. 帧格式

所有整数均为无符号整数、大端序（network byte order）。禁止直接发送 C/C++ 结构体内存。

| Offset | Size | Field | Meaning |
|---:|---:|---|---|
| 0 | 4 | Magic | ASCII `ALGN` |
| 4 | 2 | ProtocolVersion | 当前为 `1` |
| 6 | 2 | MessageType | 消息类型 |
| 8 | 4 | PayloadLength | 后续 payload 字节数 |
| 12 | 4 | RequestId | 请求/响应关联 ID |
| 16 | N | Payload | UTF-8 JSON；空消息允许长度为 0 |

- 固定头长度：16 bytes。
- 最大 PayloadLength：65,536 bytes。
- 收到大于最大长度的帧，必须立即拒绝并关闭连接。
- TCP 是字节流：读取头部和 payload 时必须循环读取到完整长度，不能假定一次 recv 就得到完整帧。
- 不接受负长度、整数溢出、错误 Magic 或不支持的协议版本。
- 第一阶段 JSON 仅用于开发握手；后续正式协议需评估二进制 schema、TLS、认证、重放保护和速率限制。

## 2. 消息类型

| Value | Name | Direction | Payload |
|---:|---|---|---|
| 1 | ClientHello | Client -> Gateway | `{"client":"AzerothLegend","build":"dev","minVersion":1,"maxVersion":1}` |
| 2 | ServerHello | Gateway -> Client | `{"server":"AzerothLegendGateway","version":1,"status":"accepted"}` |
| 3 | VersionRejected | Gateway -> Client | `{"supported":[1],"reason":"unsupported_version"}` |
| 4 | Ping | Client -> Gateway | 可为空 |
| 5 | Pong | Gateway -> Client | 可为空 |
| 6 | Error | Gateway -> Client | `{"code":"invalid_frame","message":"..."}`，不得包含敏感信息 |

未知 MessageType：发送通用 Error（若帧本身可安全解析），随后关闭连接。不要回显任意客户端 payload。

## 3. 会话状态

```text
AcceptedSocket
    -> AwaitClientHello
    -> VersionAccepted
    -> Ping/Pong
    -> Closed
```

第一阶段不允许从 VersionAccepted 转入 Authenticated 或 InWorld；这些状态尚未实现。

## 4. 超时和资源限制

- ClientHello 超时：5 秒。
- 空闲连接超时：建议 30 秒（开发阶段可配置）。
- 每连接最大 payload：65,536 bytes。
- 每连接最多允许一个未完成握手。
- 连接数上限必须可配置。
- 任何读取错误、超时或非法状态转换都应释放连接资源。
- 日志记录连接结果和错误码，不记录密码、令牌或完整敏感 payload。

## 5. 测试向量

实现测试时至少覆盖：
1. 完整合法 ClientHello -> ServerHello。
2. 协议版本不支持 -> VersionRejected。
3. Magic 错误。
4. PayloadLength 大于 65,536。
5. 头部不足 16 bytes 后断开。
6. Payload 不足声明长度后断开。
7. 未知 MessageType。
8. 握手超时。
9. 连续 Ping/Pong。
10. 客户端正常断开和异常断开。

## 6. 未来认证要求

正式认证前必须另行设计：
- Gateway 与 AzerothCore 的身份验证/账号映射接口。
- TLS 或同等强度的传输保护。
- 密码验证方式，避免明文密码落盘或写入日志。
- 会话票据生命周期、绑定、撤销和重放防护。
- 登录失败限速、审计日志和异常处理。
