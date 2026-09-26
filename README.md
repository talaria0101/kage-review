# kage vs pi vs opencode vs kimi-code: comparison review

Scope: CLI, agent engine, tools, providers, sessions, permissions, MCP, ACP,
extensibility, config, docs, tests, distribution. GUI/TUI excluded by request.
All four repos cloned 2026-09-26 and read directly; claims below cite files.
Commit pins: kage d0f535a, pi d6af72e, opencode 696f41b, kimi-code be7d5f5.
Stars read same day via gh API: pi 109381, opencode 210081, kimi-code 7670,
kage 0 (public, 0 forks). Licenses: all four MIT.

## Verdict first

kage is the best engineered of the four and the least productized. Its Rust
core, Lua capability sandbox, SSRF-hardened fetch, plain JSONL sessions, and
clean clippy/fmt/test gates beat the TypeScript trio on construction quality.
It reaches parity on providers, MCP, ACP both directions, skills, subagents,
compaction, and scriptable JSON output. It trails on everything that makes a
tool spread: installs from source only with no releases, no web search, no
LSP tool, no background or scheduled tasks, no todo or plan mode tools, no
provider OAuth login, no Windows build, no plugin marketplace, and a removed
sandbox section with nothing behind it. Default-allow permissions plus coarse
plugin capabilities make the security posture weaker than the code quality
suggests. Use it if you want a hackable single binary with real extension
isolation. Do not adopt it as a daily driver until distribution and the
missing tool classes land.

## Better: where kage leads

1. Construction. Rust workspace, 11 crates, strict downward layering
   documented in docs/reference/architecture.md. `unsafe_code = "forbid"`,
   clippy pedantic as warn, fmt clean. Measured 2026-09-26 on this machine:
   cargo fmt --check passes, cargo clippy --all-targets prints no warnings,
   kage-tools/kage-loop/kage-session unit tests pass 175 of 175 excluding 4
   environment failures described under Issues item 1.
2. Extension isolation. Lua plugins run per-plugin `_ENV` and see elevated
   APIs only when user config grant and plugin request agree
   (crates/kage-plugin/src/capabilities.rs). Recent history hardens it:
   traversal and symlink confinement for plugin writes, watchdog over plugin
   coroutines, exec-gating of MCP/ACP declares, memory caps. pi extensions,
   opencode plugins, and kimi marketplace installs have no equivalent
   per-plugin capability jail documented in the repos I read.
3. Fetch hardening. web_fetch resolves the host before the request, rechecks
   every DNS answer including redirects, rejects private/loopback/link-local
   ranges, caps at 5 redirects and 30 s total
   (crates/kage-tools/src/builtin/web_fetch.rs). This is more careful than
   the fetch tools I skimmed elsewhere.
4. Session honesty. Append-only JSONL, first line header, cat/rg friendly,
   resume/clone/fork/search subcommands, replayable (crates/kage-session).
   Same simplicity as pi session JSONL, without a sqlite backend to operate.
5. Config trust model. Project `.kage/config.toml` mcp/permissions/capability
   settings stay ignored until `kage trust`, and editing them re-asks. Denies
   survive session permission modes (commits ce2c2d2, f3bf78d era). Sensible
   and rare.
6. One engine, three frontends. TUI, `kage rpc` (ACP), and `kage -p`
   (print, optional --json event stream) drive the same dispatcher/thread
   engine with addressed, sequenced event envelopes. ACP agent and ACP client
   both exist, so editors can drive kage and kage can drive another agent.

## Good: parity, done well

Providers: 4 built-in ids (anthropic, openai, openai-responses, gemini) plus
16 OpenAI-compatible ids including zai, zai-coding-plan, zhipu-cpu
coding-plan, deepseek, groq, mistral, cerebras, xai, openrouter,
fireworks-ai, moonshotai, kimi-for-coding, and four xiaomi endpoints
(docs/guide/providers.md). Bundled models.dev snapshot plus
`kage models refresh`. This matches the class: pi has the widest catalog at
46 provider files, opencode rides models.dev, kimi centers on Moonshot OAuth
plus compatibles. Notable kage plus: ZAI coding-plan region split and
thinking-level mapping per provider family (Anthropic effort/budget, OpenAI,
Gemini thinkingLevel/Budget).

MCP: tools, resources with @server:uri mentions, prompts as /server:prompt
commands, OAuth login for remote servers, sampling gate, kage itself serving
its tools over stdio. Full-duplex coverage equal to the others.

Agents: general and explore definitions, parallel spawn when a message holds
only agent calls, depth cap (default 1, max 3), width cap (default 4, max
16), 20000 char result cap with session pointer for the rest, live agent
cards in the TUI stream. Equivalent to pi/opencode/kimi subagents in shape.

Compaction: threshold 0.8 of context, keep 4 recent turns, summarize-to-user
message framed to satisfy strict role ordering on ZAI/GLM and Anthropic.
The role-ordering care is a real detail competitors gloss over.

Permissions: per-tool allow/ask/deny with deny-first glob evaluation, per-MCP
server actions, unknown MCP tools ask by default, session approvals, always
allow persisted by rewriting only the keys involved. Opt-in path confinement
and opt-in bash env scrubbing ([bash] scrub_env, newest commit d0f535a).

Hygiene gates in CI (.github/workflows/ci.yml): fmt, lua-types drift check,
ASCII-only source rule via xtask, clippy -D warnings, matrix tests. The
generated Lua type stub (plugins/types/kage.lua) is checked against the tree
spec, same shape as fmt. Unusual and good.

## Bad: behind at least one rival

1. Distribution. kage installs from source only: Rust 1.87 plus C compiler,
   cargo build --release, symlink by hand; nix flake as the only shortcut
   (README.md). No releases published, no npm/brew/scoop/pacman entries, no
   install script, no standalone binary. pi ships npm plus standalone
   binaries with a build-binaries script. opencode ships install script plus
   npm, brew, scoop, choco, pacman, mise. kimi-code ships a one-command
   install script and npm with no Node required. This is the largest adoption
   blocker.
2. No web search tool. Builtins are read, write, edit, bash, grep, find, ls,
   web_fetch (crates/kage-tools/src/builtin/mod.rs). opencode has websearch
   plus mcp-websearch; kimi has webbridge and datasource plugins. Fetch
   without search forces the user to supply URLs.
3. No LSP or symbol tool. opencode ships packages/opencode/src/tool/lsp.ts
   plus an lsp/ service (diagnostics, symbols). kage grep/ls/find cannot
   answer go-to-definition or workspace-diagnostic questions. The only "lsp"
   hits in kage are the Lua language-server stub installer for plugin
   authors, which is a different thing.
4. No background tasks, cron, or scheduled work. opencode has
   packages/opencode/src/background/. kimi has agent-core-v2 cron tools and
   services. kage has neither; long runs block the session.
5. No todo, plan mode, or question tools. opencode ships todo, plan,
   plan-enter/exit, question. kimi ships todo, plan, tower mission/plan/
   review, swarm. kage tracks compaction and agents only, so multi-step plans
   live entirely in conversation context.
6. No provider OAuth login. `kage auth login` saves API keys prompted without
   echo; the credential store has an OAuth record shape but no provider OAuth
   flow is documented or wired (crates/kage-cli/src/auth.rs,
   docs/guide/providers.md keyed on *_API_KEY env vars). pi, opencode, and
   kimi all sign in via OAuth somewhere (Copilot, Zen, Moonshot device flow).
7. Platform support. README promises Linux, macOS, WSL. No native Windows
   story. All three rivals install and run on Windows.
8. No marketplace or discovery. Skills exist as SKILL.md loaders (user,
   project .kage, .agents) and plugins have examples plus typed stubs, but
   there is no registry, search, or one-command install. kimi has
   plugins/marketplace.json with versioned official plugins. pi documents
   extensions and packages. opencode documents plugins and skills discovery.
9. Sandbox story removed. Commit e02897c removed the unused [sandbox]
   section; docs carry sandbox notes around io removal and exec gating. What
   remains is opt-in path confinement (default off) and env scrubbing
   (default empty) on top of default-allow tool execution. pi documents
   containerization patterns, opencode has containers/, kimi inherits sandbox
   discussion. kage currently has no isolation story to point at.

## Worse: structural risks

1. One maintainer, pre-1.0, declared motion ("Status: pre-1.0. Things still
   move", README.md). Zero stars and forks means no external review base and
   no compatibility pressure. Every config key and Lua API may rename.
2. Security posture lags code quality. Defaults are allow-everything for
   builtins, confinement off, scrub list empty, and plugin capabilities are
   coarse whole-token grants (exec means any binary, env means any variable,
   net means any host passing the safety check, per capabilities.rs docs).
   Careful sandbox code wrapped in a permissive default still autoruns.
3. Agent containment is thin. Agent sessions inherit cloned permission gates
   but get no plugin runtime or MCP manager of their own (architecture.md),
   depth tops out at 3, replies truncate at 20000 chars. Fine for explore
   agents, brittle for deep general-agent trees.
4. ACP future depends on a draft. rpc/mod.rs cites draft RFD PR 1992 for
   subagent sessions. If the ACP spec lands differently, the subagent
   projection gets rewritten.
5. Measurements are mine, once, on one machine. No benchmarks were run
   against the other three (no harness was set up for that), so performance
   claims in any direction would be invented. Treat all speed language in
   this report as absent by design.

## Issues needing addressing, in priority order

1. Four web_fetch tests fail in a sandboxed host. `cargo test -p kage-tools`
   2026-09-26: 108 pass, 4 fail, all in builtin::web_fetch::tests, all
   panicking at TcpListener::bind("127.0.0.1:0") with PermissionDenied
   (web_fetch.rs test helper `serve`, lines near 199 and 281). Cause is the
   sandbox refusing loopback bind, not product logic (the SSRF rejections
   without sockets pass). Still, tests that cannot run in a sandbox will fail
   in exactly the CI-like environments contributors use. Suggest a bind
   failure skip with a clear message, or a socket-free redirect test path.
2. Publish releases and binaries. Minimum: versioned GitHub releases with
   linux x64/aarch64 and macOS archives, checksums, and an install script.
   Then package managers. Source-only install contradicts the "small enough
   to read, ready to run" pitch and blocks every non-Rust user.
3. Decide the Windows story explicitly. Either support it or document refusal
   with reasons. "Linux, macOS and WSL" leaves the largest desktop platform
   ambiguous.
4. Add web search or document its absence as a principle. A hardened fetch
   with no search is half a web story. If search is delegated to MCP, ship a
   recommended server in the docs and the doctor hints.
5. Add an LSP or symbol tool, or a documented MCP recommendation. Without
   symbols/diagnostics the agent edits blind on large repos while opencode
   answers those queries natively.
6. Add background execution plus todo/plan tracking, or state the rejection.
   These four tool classes (background, cron, todo, plan/question) are the
   entire gap between kage builtins (8 tools plus agent) and opencode (20+
   including lsp, todo, plan, question, skill) and kimi (todo, plan, tower,
   swarm, cron). Each missing class is a workflow kage cannot do: searchless
   browsing, definition-accurate edits, unattended runs, scheduled runs,
   resumable multi-step plans.
7. Tighten defaults or own the permissiveness. Either default bash to ask
   with a starter allowlist, default confine_paths on for project sessions,
   and split the coarse exec/env/net grants; or write down that kage is an
   allow-by-default power tool and say who should not run it. Current state
   is permissive defaults plus excellent jail code, which reads as unfinished.
8. Restore a sandbox story to replace the removed [sandbox] section. Even a
   documented Docker/micro-VM pattern like pi containerization.md would
   close the hole. The e02897c removal with only exec-gating notes left
   behind is a regression in documentation if not in code.
9. Wire provider OAuth or remove the OAuth credential shape. An OAuth enum
   variant with no login flow invites a half integration. GitHub Copilot,
   Zen-style, and Moonshot-style logins are the flows users now expect.
10. Build the plugin/skill registry story. Versioned marketplace index,
    trust levels at install time (kimi shows these), and doctor verification
    would convert the best-in-class isolation into network effects. Today the
    isolation protects seven example plugins.
11. Track the ACP subagent draft visibly. Pin the draft version supported and
    file the follow-up for the ratified form, since rpc/mod.rs already names
    the dependency.
12. Docs preview needs bun (docs/package.json, bun.lock) while the product
    is proudly non-Node. Either vendor a plain build or say bun is required
    for contributors. Minor, but it is the first contributor papercut.
13. No telemetry and no evals. Privacy positive, but pi ships telemetry
    contracts and evals, kimi ships telemetry, opencode ships stats. At least
    document the no-telemetry stance as a guarantee so it reads as a choice.
14. Confirm the check-ascii and lua-types gates run green for outside
    contributors on stable toolchains. They passed here via CI config
    reading, not by execution, except fmt and clippy which I ran.

## Method note

Read order: README, docs/guide, docs/plugins, docs/reference/architecture.md,
then builtin tool registry, provider registry and catalog, loop config and
compaction, permissions gate, session store, plugin capabilities and events,
RPC/ACP projection, CLI help output, CI workflow, then the same breadth skim
for pi (packages/ai providers, coding-agent tools/docs/session-format),
opencode (packages/opencode tool/auth/agent/background/lsp/mcp/plugin dirs),
kimi-code (agent-core-v2 features/tool contract, marketplace, ACP server,
auth, sessions). Ran on kage only: fmt, clippy, unit tests for three crates,
doctor, help. No TUI interaction, no model calls, no benchmarks. GUI
excluded throughout as instructed.
