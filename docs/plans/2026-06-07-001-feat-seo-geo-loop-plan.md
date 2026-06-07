# SEO + GEO Loop for compound-engineering-slim

## Context

The fork (`github.com/OutlineDriven/compound-engineering-slim`) is a deliberately slimmed fork of `EveryInc/compound-engineering-plugin`, but its public surfaces don't say so: no "why this fork" section, manifests point `homepage`/`repository` at upstream, keywords advertise removed capabilities (`image-generation`, `rails`, `ruby`), no `llms.txt`, GitHub topics empty, and the root README cites a stale component count ("14 skills"; actual 16). The user wants a full SEO + GEO loop run, with positioning that the fork is **unique and efficient**.

**Settled user decisions:**
1. "Efficient" backed by the measured component delta only — fork 16 skills / 16 agents / 5 converter targets vs upstream's 39 skills / 43 agents (both at v3.11.2). No token/speed/memory claims (unmeasured — forbidden).
2. Manifest `homepage`/`repository` repoint to the fork. (Noted once: the every.to article is a legitimate philosophy link; user chose full repoint over split. Honor it; lineage stays acknowledged in README prose.)
3. Surface scope: repo files + GitHub meta (`gh repo edit`). No external publishing.

**Verified constraints (Plan-agent verified against source):**
- `bun run release:validate` (`scripts/release/validate.ts` → `src/release/metadata.ts`) owns only `version`/`metadata.version` and the `description` constants (asserted at `tests/release-metadata.test.ts:133`). **Do not touch version or description.** `homepage`, `repository`, `keywords`, `tags` are never read or asserted — safe to edit.
- No test asserts README prose, keywords, homepage, repository, or tags. Contract tests cover skill SKILL.md content only — untouched here.
- `.cursor-plugin`/`.codex-plugin` manifests are hand-maintained; edits must be applied to each file.
- `gh` is authed; repo has `repositoryTopics: null` and an existing About description to refine.
- `main` is protected — feature branch + PR required.

## Execution Preamble

- Create feature branch (e.g., `seo-geo-loop`).
- Persist this plan as the durable ce-plan artifact: `docs/plans/2026-06-07-001-feat-seo-geo-loop-plan.md` (plan-mode restricted writes; copy on execution start).

## Implementation Units

### U1. Manifest metadata fixes
**Goal:** Repoint `repository`+`homepage` to `https://github.com/OutlineDriven/compound-engineering-slim`; refresh keywords/tags to actual capabilities. Leave `version`/`description` untouched.
**Files:**
- `package.json` (`repository`, `homepage`)
- `plugins/compound-engineering/.claude-plugin/plugin.json` (`keywords`, `repository`, `homepage`)
- `plugins/compound-engineering/.cursor-plugin/plugin.json` (same; keep leading `cursor`, `plugin` entries)
- `plugins/compound-engineering/.codex-plugin/plugin.json` (same + `interface.websiteURL`)
- `.claude-plugin/marketplace.json` (`plugins[0].tags`, `plugins[0].homepage`)
**Keywords refresh:** drop `image-generation`, `rails`, `ruby`; set `ai-powered`, `compound-engineering`, `code-review`, `workflow-automation`, `knowledge-management`, `planning`, `debugging`, `ai-agents`, `developer-tools`, `claude-code`, `codex`.
**Verification:** `bun run release:validate` green; `bun test` green; diff shows only keyword/repository/homepage/tags lines.
**Commit:** `fix(plugin-manifest): repoint repository/homepage to fork and drop stale keywords` (intent = fixing wrong data; never `!`).

### U2. Root README — positioning + "Why this fork" + stale-count fix
**Goal:** Add a fork-differentiation section after the Philosophy block; fix "14 skills" → "16 skills" (README.md:68 area).
**Files:** `README.md`
**Approach:** Concise "Why this fork" section with a comparison table — quotable, numeric, GEO-friendly:
- 16 skills vs upstream 39; 16 agents vs upstream 43; 5 converter targets (opencode/codex/pi/gemini/kiro; copilot/droid dropped, −1,722 LOC); coding-tutor plugin removed (−1,244 LOC); 92+ agent personas consolidated; removals tracked in `src/utils/legacy-cleanup.ts` registries.
- **Date the upstream snapshot** ("as of June 2026, upstream ships 39 skills / 43 agents") — upstream's counts are not ours to maintain; an undated number is the same staleness bug as the "14 skills" being fixed. Applies to llms.txt (U4) too.
- "Efficient" framed strictly as surface-area: fewer components → smaller install + prompt surface, same brainstorm→plan→work→review→compound core. **No runtime claims.**
- Acknowledge upstream lineage with link (attribution lives here, not in manifests).
- FAQ entry: "How is this different from upstream compound-engineering?" — answer-shaped for AI-engine extraction (GEO).
**Verification:** counts match `ls plugins/compound-engineering/{skills,agents}`; `bun test` green.
**Commit:** `docs(readme): add fork comparison section and fix stale component count`.

### U3. Plugin README — 5-minute quickstart (developer-onboarding skill)
**Goal:** TTFV-shaped quickstart at top of `plugins/compound-engineering/README.md`: prerequisites up front, copy-paste install, `/ce-setup`, first success moment (run `/ce-plan` or `/ce-code-review` on a real change), "what you built", next steps.
**Files:** `plugins/compound-engineering/README.md`
**Dependencies:** U7 (quickstart must use the corrected install path).
**Verification:** every command copy-paste-runnable **and installs this fork**; counts stay 16/16; `bun test` green.
**Commit:** `docs(readme): add 5-minute quickstart to plugin README`.

### U7. Fix documented install path — converter commands currently install UPSTREAM
**Goal:** All 15+ `bunx @every-env/compound-plugin ...` lines in `README.md` resolve via npm to upstream's published CLI (fork's `package.json` carries upstream's name; no publish workflow exists in `.github/workflows/`). The npm badge (README.md:4) likewise displays upstream's version. A reader following the converter install docs gets the 39-skill upstream product — directly contradicting the U2 positioning at the moment of install.
**Files:** `README.md` (lines 4, 104, 115, 134, 148, 156-184, 298-316 area)
**Approach:**
- Replace npm-routed `bunx @every-env/compound-plugin` invocations with a fork-sourced form. Try `bunx github:OutlineDriven/compound-engineering-slim install compound-engineering --to <target>` first (`bin` is defined; bun runs TS natively) — **verify it actually works at execution time**; if not, fall back to the already-documented clone + `bun run src/index.ts install ./plugins/compound-engineering --to <target>` pattern (README.md:215).
- Remove or replace the npm badge (it reports upstream's version on the fork's README).
- Marketplace-add lines (`OutlineDriven/compound-engineering-slim`) are already correct — untouched.
**Verification:** run one converter install end-to-end via the documented command on a scratch target and confirm the installed bundle has 16 skills (not 39); `git grep -c '@every-env/compound-plugin' README.md` returns 0 (or only inside an explicit cleanup-of-legacy-installs note, where the upstream package name is the historically correct cleanup tool).
**Commit:** `fix(readme): route converter install commands through the fork instead of upstream npm` (a regression test written today — "documented install yields 16 skills" — fails before this change: `fix:`).

### U4. llms.txt (GEO artifact)
**Goal:** Root `llms.txt` in llmstxt.org shape: H1 + blockquote summary (with the numeric delta), sectioned links to READMEs, skills/agents inventory, philosophy.
**Files:** `llms.txt` (new, repo root)
**Honesty note carried in plan:** no hosted site serves it — value is for agents/humans reading the repo tree and raw.githubusercontent fetches, not crawler auto-discovery.
**Verification:** links resolve as repo-relative paths; `bun test` green (converter never reads root `llms.txt`).
**Commit:** `docs(seo): add llms.txt`.

### U5. SEO/GEO loop runbook (the durable loop definition)
**Goal:** `docs/seo-geo-loop.md` — forward-looking runbook (fits neither `docs/solutions/` past-problems nor `docs/plans/`):
- **Target queries:** "compound engineering claude code plugin", "slim compound engineering fork", "claude code code review agents", "claude code plugin fewer agents", "convert claude code plugin to codex/opencode/gemini".
- **Probe procedure:** Google `site:` + plain queries; AI-engine probes (ask Claude/ChatGPT/Perplexity "what is the slim fork of compound-engineering?", "lightweight alternative to compound-engineering plugin") via available web tools.
- **Baseline table:** date | query | engine | result/citation | notes.
- **Iteration cadence:** re-probe + refresh README/topics on each notable inventory change or release.
**Files:** `docs/seo-geo-loop.md` (new)
**Commit:** `docs(seo): add SEO/GEO loop runbook`.

### U6. Run the loop, iteration 1 (execution-time)
**Goal:** Close the loop the user asked to "fully run":
1. **GitHub meta (outward-facing — confirm with user before running):**
   `gh repo edit OutlineDriven/compound-engineering-slim --description "Slim fork of the Compound Engineering plugin — 16 skills, 16 agents for Claude Code, Codex, Cursor, OpenCode, Pi, Gemini, Kiro" --add-topic claude-code --add-topic claude-code-plugin --add-topic codex --add-topic cursor --add-topic opencode --add-topic gemini-cli --add-topic code-review --add-topic ai-agents --add-topic developer-tools --add-topic agentic-workflows`
2. **Baseline probes:** run the U5 probe list (web search read-only), record results in the runbook baseline table.
3. Commit baseline: `docs(seo): record GEO baseline, iteration 1`.
**Verification:** `gh repo view --json repositoryTopics,description` reflects new values; baseline table populated.

## Deferred / Open

- **npm identity:** fork's `package.json` still carries upstream's name `@every-env/compound-plugin` (not private, no publish workflow). U7 fixes the *documented* install path; whether to rename and publish the fork's CLI under its own npm name is a separate product decision for the user.
- `description` constants stay as-is (changing them costs a lockstep `src/release/metadata.ts` + test edit for no stated benefit).
- External publishing (directory listings, awesome-lists, posts) — out of scope per user decision; future loop iterations may revisit.

## Verification (whole campaign)

1. `bun run release:validate` green after every unit.
2. `bun test` green (full suite) before PR.
3. All numeric claims in README/llms.txt re-checked against `ls` of skills/agents dirs and upstream's live counts.
4. PR to `main` (protected; `test` check required) — no direct push, no `!`/BREAKING markers.
5. Post-merge: `gh repo view` confirms topics/description; baseline table has ≥1 row per target query.
