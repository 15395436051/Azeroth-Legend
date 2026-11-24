# 第一阶段实施计划：UE5 客户端基础与网关握手

> 状态：实施计划。当前不包含真实账号认证、角色进入世界或游戏玩法。
> 目标引擎：Unreal Engine 5.5；目标客户端：Windows；服务端基础：AzerothCore 3.3.5a。

## 1. 已确认的项目决策

- 使用现有仓库 `15395436051/Azeroth-Legend`，从独立分支开发，不直接修改 `main`。
- 原版 WoW 3.3.5a 客户端仅作为本地资源来源；不得将 MPQ、模型、地图、贴图、音频或提取文件提交到公开仓库，除非明确拥有再分发许可。
- UE5 客户端不直接冒充 WoW 3.3.5a 客户端连接 AuthServer/WorldServer。
- 采用仓库既定架构：UE5 Client -> 自定义 TCP Gateway -> AzerothCore Adapter -> AzerothCore。
- Gateway 第一阶段只做协议版本握手和无害测试消息；不传输真实账号密码，不声称认证或游戏功能已实现。
- 游戏状态、移动合法性、战斗、掉落、背包和持久化最终由服务端权威决定。

## 2. 第一阶段验收目标

1. 创建并编译最小 UE5.5 C++ 桌面项目。
2. 创建独立 Gateway TCP 原型。
3. 定义跨平台、明确字节序的消息帧格式。
4. UE5 客户端能够连接 Gateway 并完成版本握手。
5. Gateway 对错误版本、未知消息、超长帧、截断帧和断线进行安全处理。
6. 提供自动化协议测试和运行说明。
7. 不影响现有 AzerothCore 生产服务和数据库。

## 3. 推荐仓库结构

```text
client/
  AzerothLegend/
    AzerothLegend.uproject       # 由 UE 5.5 创建
    Source/
      AzerothLegend/
        AzerothLegend.Build.cs
        Public/
          Network/               # TCP 客户端与协议接口
          UI/                    # 后续登录 UI
        Private/
          Network/
          UI/
    Config/
    Content/                     # 不提交第三方受限游戏资源
gateway/
  CMakeLists.txt
  include/legend/protocol/
  src/
  tests/
tools/
  protocol-test-client/
docs/
  04-phase1-implementation-plan.md
  protocol/
    frame-v1.md
```

## 4. 初始协议帧（草案）

采用显式序列化，不直接发送 C++ struct 内存布局。

| 字段 | 长度 | 说明 |
|---|---:|---|
| Magic | 4 bytes | 固定 ASCII 标识 `ALGN` |
| ProtocolVersion | 2 bytes | 无符号整数，大端 |
| MessageType | 2 bytes | 无符号整数，大端 |
| PayloadLength | 4 bytes | 无符号整数，大端 |
| RequestId | 4 bytes | 无符号整数，大端 |
| Payload | 可变 | 最大长度由协议常量限制 |

第一阶段消息：
- `ClientHello`：客户端协议版本和构建标识。
- `ServerHello`：服务端支持的版本和能力位。
- `VersionRejected`：版本不兼容。
- `Ping` / `Pong`：无害连通性测试。

实现时必须在 `docs/protocol/frame-v1.md` 固化消息编号、字节序、最大帧长度、字符串编码、超时和错误处理。协议尚未安全审查，不用于公网账号认证。

## 5. 实施顺序

### 任务 A：源码与环境审计
- 记录 AzerothCore 精确 commit、分支、模块和本地补丁。
- 记录 UE 5.5、Visual Studio 2022、Windows SDK、CMake 和编译器版本。
- 确认现有服务器地址、端口和配置位置；文档中只记录脱敏信息，不记录密码或令牌。
- 准备独立开发环境和数据库备份，不在生产服试验。

### 任务 B：Gateway 原型
- 使用 C++17/20（以本机工具链验证结果为准）和 CMake。
- 实现 TCP accept、长度受限的帧读取、显式序列化和版本握手。
- 为合法帧、错误 Magic、错误版本、未知消息、超长长度、截断帧和断线添加测试。
- 增加连接超时、日志脱敏和每连接资源上限。

### 任务 C：UE5.5 客户端壳
- 用 UE 5.5 创建 C++ Desktop 项目。
- 创建独立 Network 模块/目录；不要把 socket、帧解析和 UMG 放在同一个类。
- 先实现连接状态和版本握手测试界面。
- 网络 I/O 不阻塞 Game Thread；断线、超时和销毁时正确清理 Socket。
- UE 客户端只发送测试握手，不发送真实账号密码。

### 任务 D：联调
- 本机启动 Gateway。
- UE5 客户端连接 `127.0.0.1` 的开发端口。
- 显示 Connected、Version Accepted 或 Version Rejected。
- 验证断线重连、服务端退出和协议错误提示。

## 6. 第一阶段明确不做

- 不直接实现 WoW SRP6 登录或直接连接 WoW AuthServer/WorldServer。
- 不实现真实账号密码登录、Realm 列表、角色列表或进入游戏。
- 不改 AzerothCore 核心，不修改生产数据库。
- 不导入或提交原版客户端资源。
- 不实现地图、角色模型、怪物、战斗和背包。

真实认证需要先确定 Gateway 到 AzerothCore 的身份验证/会话适配方案，并完成安全设计；不能把明文密码放进未经保护的 TCP 测试协议。

## 7. 完成定义

第一阶段只有在以下条件全部满足时才算完成：
- Gateway 可独立构建和启动。
- 自动化协议测试通过。
- UE5.5 项目可在 Visual Studio 2022 中生成并编译。
- UE5 客户端与 Gateway 完成版本握手。
- 错误输入不会导致崩溃或无界内存分配。
- README/文档说明运行命令、端口配置和已知限制。
- 没有生产凭据、数据库备份或受限游戏资源进入 Git。
