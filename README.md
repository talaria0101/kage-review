# kage vs pi vs opencode vs kimi-code

kage is the best engineered of the four and the least productized. Its Rust
core, Lua capability sandbox, hardened web fetch, plain JSONL sessions, and
clean clippy/fmt/test gates beat the TypeScript trio on construction. It
matches them on providers, MCP, ACP, skills, subagents, compaction, and
scriptable output. It trails on everything that spreads a tool: source-only
install with no releases, no web search, no LSP tool, no background or
scheduled tasks, no todo or plan tools, no provider OAuth, no Windows build,
no marketplace, and a removed sandbox section with nothing behind it.
Default-allow permissions plus coarse plugin grants make the posture weaker
than the code. Good choice for a hackable single binary with real extension
isolation. Not a daily driver until distribution and the missing tool classes
land.

Scope: CLI, engine, tools, providers, sessions, permissions, MCP, ACP,
extensibility, config, docs, tests, distribution. GUI/TUI excluded.

## Snapshot

| | kage | pi | opencode | kimi-code |
| --- | --- | --- | --- | --- |
| Repo | QaidVoid/kage | earendil-works/pi | anomalyco/opencode | moonshotai/kimi-code |
| Pin reviewed | d0f535a | d6af72e | 696f41b | be7d5f5 |
| Stars (2026-09-26) | 0 | 109381 | 210081 | 7670 |
| Forks | 0 | 13900 | 27775 | 1256 |
| License | MIT | MIT | MIT | MIT |
| Language | Rust, Lua vendored | TypeScript monorepo | TypeScript (bun), Go bits | TypeScript (pnpm) |
| Install | source only, Rust 1.87 + C compiler | npm + standalone binaries | install script, npm, brew, scoop, choco, pacman, mise | install script, npm, no Node needed |
| Releases published | none | yes | yes | yes |
| Platforms | Linux, macOS, WSL | Linux, macOS, Windows | Linux, macOS, Windows | Linux, macOS, Windows |
| Status | pre-1.0, single maintainer | community, auto-close triage | large community | vendor led (Moonshot AI) |

## Standing per dimension (kage vs the named rivals)

Legend. Better = kage ahead of every rival named in that row. Good = kage
level with the named rivals, plus where noted. Bad = kage behind at least
one named rival. Worse = behind with structural weight, not a small gap.
Where a cell says unverified, I did not confirm that rival's position, so no
claim is made against them there.

| Dimension | Verdict for kage | Against whom | Note |
| --- | --- | --- | --- |
| Construction quality | Better | ahead of pi, opencode, kimi-code | Rust, unsafe forbidden, pedantic clippy clean, fmt clean, layered crates |
| Extension isolation | Better | ahead of pi, opencode, kimi-code | per-plugin Lua _ENV + capability jail, traversal/symlink confinement, watchdog; kimi shows install trust levels but no jail |
| Fetch hardening | Better | ahead of pi, opencode, kimi-code on documented depth | resolve-before-request SSRF checks on every DNS answer, redirect + timeout caps |
| Session format | Better | ahead of opencode, kimi-code; level with pi | append-only JSONL, cat/rg friendly, resume/clone/fork/search; opencode and kimi use sqlite-family stores, pi uses JSONL |
| Config trust | Better | ahead of pi, opencode, kimi-code | project config ignored until `kage trust`, edits re-ask, denies survive modes; no equivalent flow found in the others |
| One engine, three frontends | Good | level with opencode, pi; plus over kimi-code | TUI, ACP rpc, and `-p` print share one dispatcher with sequenced events; plus is ACP in both directions (agent + client) |
| Providers | Good | level with pi, opencode, kimi-code | 20 ids vs pi 46 files (widest), opencode models.dev, kimi Moonshot-first; kage plus is ZAI region split + thinking mapping |
| MCP | Good | level with pi, opencode, kimi-code | tools, resources, prompts, OAuth covered everywhere |
| ACP | Good | level with opencode, kimi-code; ahead of pi | both directions in kage; pi surface is RPC commands, no ACP tree found |
| Agents/subagents | Good | level with pi, opencode, kimi-code | general + explore, parallel spawn, depth/width caps, 20k result cap; same shape everywhere |
| Compaction | Good | level with pi, opencode, kimi-code, slight plus | 0.8 threshold; plus is role-order safe summaries for ZAI/GLM + Anthropic |
| Permissions model | Good | level with opencode, kimi-code; pi differs by design | allow/ask/deny, deny-first globs, per-server MCP actions, session approvals; pi has no builtin gate and uses containers instead |
| CI hygiene | Good | level with pi, opencode, kimi-code on basics; ahead on two gates | fmt, clippy -D warnings, matrix tests everywhere; lua-types drift + ASCII gates are kage-only |
| Distribution | Bad | behind pi, opencode, kimi-code | no binaries, no install script, no package managers; all three ship releases |
| Web search | Bad | behind opencode, kimi-code; pi unverified | fetch only, no search tool; opencode ships websearch, kimi has webbridge/datasource |
| Code intelligence | Bad | behind opencode; pi, kimi-code unverified | no LSP/symbol/diagnostic tool; opencode ships lsp.ts + service |
| Background + scheduled work | Bad | behind opencode, kimi-code; pi unverified | no background tasks, no cron; opencode has background/, kimi has cron |
| Plan tracking | Bad | behind opencode, kimi-code; pi unverified | no todo, plan mode, or question tools; both rivals ship them |
| Provider login UX | Bad | behind pi, opencode, kimi-code | API keys only, no OAuth flow; each rival signs in via OAuth somewhere |
| Windows | Bad | behind pi, opencode, kimi-code | not supported; all three install and run there |
| Plugin discovery | Bad | behind kimi-code, opencode, pi | no registry or marketplace; kimi has a versioned index, others document discovery |
| Sandbox story | Worse | behind pi, opencode, kimi-code | section removed, confinement opt-in and off by default; pi documents containers, opencode has containers/ |
| Security defaults | Worse | behind opencode, kimi-code; pi differs by design | default-allow builtins, coarse whole-token grants vs ask-leaning rivals; pi pushes the boundary to containers |
| Maturity risk | Worse | behind pi, opencode, kimi-code | pre-1.0, one maintainer, 0 stars/forks vs communities and a vendor |

## Builtin tools

| Tool | kage | pi | opencode | kimi-code |
| --- | --- | --- | --- | --- |
| read | yes | yes | yes | yes |
| write | yes | yes | yes | yes |
| edit + diff | yes | yes | yes | yes |
| bash/shell | yes, 120 s default, output cap, opt-in env scrub | yes | yes | yes |
| grep | yes | yes | yes | yes |
| find/glob | yes | yes | yes (glob) | yes |
| ls | yes | yes | n/a (via glob/bash) | n/a |
| web fetch | yes, SSRF hardened | yes | yes (webfetch) | yes |
| web search | no | varies | yes + mcp-websearch | via webbridge/datasource plugins |
| LSP/symbols/diagnostics | no | varies | yes | varies |
| todo tracking | no | varies | yes | yes |
| plan mode | no | varies | yes, plan-enter/exit | yes, plan + tower |
| question prompt | no | varies | yes | varies |
| background tasks | no | varies | yes | via cron |
| scheduled (cron) tasks | no | no | varies | yes |
| skill loading | yes (SKILL.md) | varies | yes | yes |
| subagents | yes (agent tool) | yes | yes | yes (coder/explore/plan + swarm) |
| apply_patch | via edit | varies | yes | varies |

kage registry: `crates/kage-tools/src/builtin/mod.rs` lists read, write,
edit, bash, grep, find, ls, web_fetch. That is the whole builtin set plus
the `agent` tool.

## Providers and auth

| | kage | pi | opencode | kimi-code |
| --- | --- | --- | --- | --- |
| Provider count | 4 native + 16 compat ids | 46 provider files, widest catalog | models.dev driven | Moonshot first + compatibles |
| Model catalog refresh | yes, `kage models refresh` | yes, generate-models | yes | yes |
| ZAI coding-plan split | yes, global + CN regions | yes files | varies | varies |
| Xiaomi + Kimi compat ids | yes | yes files | varies | native |
| API key auth | yes, env or `auth login` | yes | yes | yes |
| Provider OAuth login | no (shape exists, no flow) | yes (Copilot etc.) | yes (Zen etc.) | yes (Moonshot device flow) |
| Credential file mode | 0600 auth.json | varies | varies | varies |
| Thinking levels | per family mapping | varies | varies | varies |

## Sessions, permissions, protocols

| | kage | pi | opencode | kimi-code |
| --- | --- | --- | --- | --- |
| Session storage | JSONL, header first line | JSONL + sqlite backend | sqlite/effect stores | backends + minidb |
| resume/clone/fork/search | yes, subcommands | yes | yes | yes |
| Permission actions | allow/ask/deny + session approvals | sandbox/container patterns, no builtin gate | rules + arity prefixes | rules + approval dialogs |
| Builtin default | allow | n/a | ask-leaning | ask-leaning |
| Unknown MCP tools | ask | varies | varies | varies |
| Path confinement | opt-in, default off | via container | varies | varies |
| MCP tools/resources/prompts/OAuth | yes | yes | yes | yes |
| ACP agent (editors drive it) | yes | via RPC entries | yes | yes (`kimi acp`) |
| ACP client (drives others) | yes | varies | varies | varies |
| Scriptable `-p` + `--json` stream | yes | yes | yes | yes |
| `doctor` diagnostics | yes | varies | varies | varies |

## Extensibility

| | kage | pi | opencode | kimi-code |
| --- | --- | --- | --- | --- |
| Language | Lua (vendored 5.4) | TypeScript extensions | TypeScript plugins | plugins + skills |
| Isolation | per-plugin _ENV, capability grants | host permissions | plugin sandbox varies | trust level shown at install |
| Capabilities | session_write, exec, env, net, crypto (coarse, whole-token) | varies | varies | varies |
| Hooks/autocmds | tool_call, session, provider-request events | session/agent hooks | plugin hooks | lifecycle external hooks |
| Skills (SKILL.md) | yes, user + project + .agents | varies | yes + discovery | yes + marketplace |
| Marketplace/registry | no | docs + packages | docs + discovery | yes, versioned official index |
| Slash commands | yes | yes | yes | yes |
| Typed authoring stubs | yes, kage.lua drift-checked | varies | varies | varies |

## Issues, in priority order

| # | Issue | Evidence | Fix |
| --- | --- | --- | --- |
| 1 | 4 web_fetch tests fail where loopback bind is denied | `cargo test -p kage-tools` 2026-09-26: 108 pass, 4 fail at `TcpListener::bind("127.0.0.1:0")`, helper `serve` near web_fetch.rs:199 and :281 | skip with message on bind failure, or socket-free redirect path |
| 2 | No releases or binaries | README install is cargo build + symlink only; `gh release list` empty | versioned archives + checksums + install script, then managers |
| 3 | Windows ambiguous | README: Linux, macOS, WSL | support it or document refusal with reasons |
| 4 | No web search | registry has fetch only | add search, or ship + document a recommended MCP server |
| 5 | No LSP/symbol tool | grep absent outside Lua-stub installer | add tooling or a documented MCP recommendation |
| 6 | No background, cron, todo, plan tools | registries and features dirs absent | add them or state the rejection; this is the whole tool gap vs rivals |
| 7 | Permissive defaults, coarse grants | default allow, confine off, scrub empty; exec/env/net are whole-token | ask-by-default bash starter list, confinement on, split grants, or own it in writing |
| 8 | Sandbox story removed | commit e02897c removed `[sandbox]` | documented container pattern like pi containerization.md |
| 9 | OAuth shape with no flow | `Credential::OAuth` in auth.rs, login saves keys only | wire provider OAuth or remove the shape |
| 10 | No plugin/skill registry | 7 example plugins, no index | versioned index + trust levels + doctor checks |
| 11 | ACP subagents track a draft | rpc/mod.rs cites draft RFD PR 1992 | pin draft version, file ratification follow-up |
| 12 | Docs preview needs bun | docs/package.json + bun.lock, product is non-Node | plain build or documented contributor requirement |
| 13 | No telemetry or evals stance | none found in tree | document no-telemetry as a guarantee |
| 14 | ascii + lua-types gates unverified outside CI | read in workflow, only fmt/clippy run here | confirm green on stable toolchains for contributors |

Test note: kage-loop (111) and kage-session (59 + 5) suites pass fully.
The 4 failures above are environmental (sandbox blocks loopback bind), not
product logic; the socket-free SSRF rejection tests pass.

## Method

Read directly, no benchmarks, no model calls, no TUI interaction. kage:
README, docs/guide, docs/plugins, docs/reference/architecture.md, tool
registry, provider registry + catalog, loop config + compaction, permission
gate, session store, plugin capabilities + events, RPC/ACP projection, CLI
help, CI workflow. Ran: fmt, clippy, three crate test suites, doctor, help.
Rivals skimmed at the same breadth: pi providers/tools/docs/session-format,
opencode tool/auth/agent/background/lsp/mcp/plugin dirs, kimi
agent-core-v2 features/tool contract/marketplace/ACP/auth/sessions. Commit
pins and star counts in the Snapshot table.
