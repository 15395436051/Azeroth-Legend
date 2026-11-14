# Contributing

Thanks for your interest in Azeroth-Legend.

## Before starting

- Read the project [README](README.md) and architecture notes in `docs/`.
- Open an issue before beginning a substantial feature or refactor.
- For large changes, discuss the design and compatibility impact first.
- Do not assume an undocumented AzerothCore API exists. Verify against the checked-out source version.

## Pull request workflow

1. Fork the repository or create a feature branch.
2. Keep each pull request focused on one logical change.
3. Include a clear summary, motivation, test steps and known limitations.
4. Update documentation when changing protocol, database schema or build instructions.
5. Do not merge unreviewed or untested changes into the default branch.

Suggested branch names:
- `docs/<topic>`
- `feature/<topic>`
- `fix/<topic>`
- `research/<topic>`

## Code and design principles

- Prefer small, isolated AzerothCore modules and adapters.
- Keep the gateway independent from the world server where practical.
- Treat the server as authoritative for gameplay state.
- Validate all client-supplied data.
- Make database migrations explicit and reversible where feasible.
- Avoid introducing unnecessary dependencies.

## Security and sensitive data

Never commit:
- Passwords, API tokens, private keys or production credentials.
- Real server addresses or private deployment configuration unless explicitly approved.
- Database dumps, player personal data, logs containing secrets or backups.
- Compiled binaries and generated build directories.

Report suspected vulnerabilities privately to the repository maintainer rather than publishing exploit details in an issue.

## Third-party assets and licensing

Only submit material that you own or are authorized to redistribute. Do not commit extracted game client assets, maps, models, textures, audio or other copyrighted content without explicit permission. Do not change or remove upstream license and copyright notices.

## Tests

Every pull request should state:
- What was changed.
- Which build or tests were run.
- The environment and relevant version.
- Any tests that could not be run and why.
