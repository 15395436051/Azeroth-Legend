# Development Roadmap

> The roadmap is provisional and depends on the source audit and available rights-cleared assets.

## Phase 0 — Source and environment audit

- [ ] Record OS, compiler, CMake and dependency versions.
- [ ] Record AzerothCore commit, branch and working-tree status.
- [ ] Identify enabled modules and local patches.
- [ ] Inspect build layout and current service processes.
- [ ] Identify database names and configuration locations without exposing secrets.
- [ ] Map relevant ScriptMgr hooks and world/entity APIs from the checked-out source.
- [ ] Document maps/vmaps/mmaps usage and asset limitations.
- [ ] Create a safe backup and a separate development environment before changes.

**Exit criteria:** an audit report exists, local modifications are understood, and a non-production test environment is available.

## Phase 1 — Minimal server module and gateway prototype

- [ ] Add a minimal `mod-legend-base` module.
- [ ] Add configuration loading and startup/shutdown logging.
- [ ] Define protocol framing and version negotiation.
- [ ] Build a separate TCP gateway prototype.
- [ ] Add tests for valid, invalid, oversized and truncated messages.

**Exit criteria:** the module loads without disrupting the existing server, and a test client can complete a version handshake with the gateway.

## Phase 2 — Client connectivity

- [ ] Create a minimal UE5.5 desktop project.
- [ ] Connect to the gateway.
- [ ] Display connection and protocol-version state.
- [ ] Implement a development-only test account/session flow after the security design is approved.

**Exit criteria:** the client can connect, negotiate a protocol version and report clean disconnects.

## Phase 3 — First playable vertical slice

- [ ] Enter one test area.
- [ ] Spawn and display one player character.
- [ ] Synchronize validated movement.
- [ ] Spawn one test creature.
- [ ] Implement one basic attack with server-calculated results.
- [ ] Implement one test drop and inventory update.
- [ ] Persist character state and verify it after relog.

**Exit criteria:** a repeatable end-to-end test passes in a non-production environment.

## Phase 4 — Core game systems

- [ ] Warrior, Mage and Taoist class foundations.
- [ ] Character progression and attributes.
- [ ] Skills, cooldowns and resource costs.
- [ ] Equipment, inventory and item drops.
- [ ] Monster AI and boss encounters.
- [ ] PvP, PK status and guild foundations.
- [ ] Trading and economy safeguards.

## Phase 5 — World, content and operations

- [ ] Rights-cleared environment and character asset pipeline.
- [ ] Terrain, collision, animation and coordinate validation.
- [ ] Deployment automation and monitoring.
- [ ] Load, security and recovery testing.
- [ ] Community testing and release process.

## Non-negotiable safeguards

- Do not experiment on a production realm.
- Do not overwrite the existing source tree or database.
- Do not commit secrets, database dumps or third-party copyrighted assets.
- Keep changes reviewable and reversible.
