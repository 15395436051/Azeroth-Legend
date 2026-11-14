# Azeroth-Legend

**A modern 3D MMORPG project exploring Legend of Mir-inspired gameplay on AzerothCore and Unreal Engine 5.5.**

> Project status: early planning and architecture. This repository is not a playable game yet.

[简体中文](#项目简介)

## Project vision

Build an independent 3D online RPG with:
- **Server foundation:** an adapted AzerothCore codebase, initially running on Linux.
- **Client:** a custom Unreal Engine 5.5 desktop client.
- **Gameplay direction:** Warrior, Mage and Taoist archetypes; equipment progression; skills; monster drops; PvP; guilds; bosses; and trading.
- **World direction:** evaluate the technical and legal feasibility of recreating selected world layouts and environments in the new client.

This project is not affiliated with or endorsed by Blizzard Entertainment or any other third-party rights holder.

## Architecture at a glance

`Unreal Engine 5.5 client -> custom gateway/protocol -> AzerothCore adaptation -> database`

The stock AzerothCore server speaks the World of Warcraft client protocol. A custom Unreal client cannot connect to it directly without implementing a compatible protocol or a translation layer.

Gameplay-critical decisions must remain server-authoritative, including movement validation, combat, damage, drops, inventory and persistence.

AzerothCore server data such as `maps`, `vmaps` and `mmaps` is not a ready-to-render Unreal environment. Rendering requires a separate client asset pipeline for terrain, meshes, materials, skeletons, animations and collision.

## Repository layout

```text
server/       AzerothCore modules and server integration
gateway/      Custom client protocol gateway
client/       Unreal Engine 5.5 client
tools/        Asset conversion and developer utilities
database/     Versioned schema changes and migrations
docs/         Architecture, protocol, audit and development guides
```

## Milestones

- [ ] Audit the existing AzerothCore source, build setup, modules and database configuration.
- [ ] Document architecture and the initial network protocol.
- [ ] Add a minimal `mod-legend-base` module with configuration and startup logging.
- [ ] Build a standalone TCP gateway prototype (version handshake and test messages only).
- [ ] Connect a UE5.5 prototype client to the gateway.
- [ ] Prove one end-to-end loop: enter a test area, move, fight one creature, receive a test drop and persist character state.

See [the development roadmap](docs/03-development-roadmap.md).

## Development principles

1. Audit the existing AzerothCore version and local changes before modifying it.
2. Prefer isolated modules and adapters over invasive core changes.
3. Never overwrite a working server, database, configuration or source tree.
4. Never commit credentials, database dumps, private server data, compiled output or third-party copyrighted game assets.
5. Document protocol changes and database migrations.
6. Use issues and pull requests for collaborative changes.

## Legal and asset notice

Only contribute code, documentation and assets that you own or are licensed to redistribute. Do not upload World of Warcraft client data, extracted models, maps, textures, sounds, fonts or other third-party copyrighted content without explicit permission. Keep locally supplied game data outside this public repository.

Third-party names and trademarks remain the property of their respective owners.

---

## 项目简介

Azeroth-Legend 是一个探索性 3D MMORPG 项目，计划以 AzerothCore 作为服务端基础，以 Unreal Engine 5.5 开发全新桌面客户端，并采用偏经典传奇风格的职业、装备、技能、打怪掉落、PK、行会和 BOSS 等玩法。

### 重要技术说明

- UE5.5 客户端不能直接连接原版 AzerothCore 的 WoW 通信协议，需要设计自定义网关或协议适配层。
- 移动、战斗、伤害、掉落、背包和角色数据必须由服务端权威处理。
- 服务端的地图寻路数据不等于客户端可渲染的地图模型，需要独立的资源转换流程。
- 在完成现有源码审计前，不修改生产服务端，不覆盖原有数据库或配置。

### 当前阶段

当前仓库仅用于项目文档、架构设计和协作准备。第一步是审计现有 AzerothCore 源码与运行环境，再决定具体改造方案。
