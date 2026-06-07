# SEO/GEO Loop

This runbook keeps the fork's public surfaces (READMEs, manifests, GitHub About/topics, llms.txt) accurate and discoverable by search engines and AI answer engines. The loop is: audit the surfaces against the current inventory -> optimize the wording and metadata -> probe how the repo surfaces in search and answer engines -> iterate on what the probe reveals. The goal is factual discoverability for `OutlineDriven/compound-engineering-slim`, not displacing the upstream `EveryInc/compound-engineering-plugin` it forks from.

## Owned surfaces

| surface | file/location | what to keep fresh |
| --- | --- | --- |
| root README | `README.md` | component counts, the slim-vs-upstream comparison table, install commands |
| plugin README | `plugins/compound-engineering/README.md` | quickstart, inventory of skills and agents |
| manifests | `.claude-plugin/marketplace.json`, `plugins/compound-engineering/.claude-plugin/plugin.json` | `keywords` and `homepage` only; `version` and `description` are release-owned, hands off |
| llms.txt | `llms.txt` | inventory names plus the delta against upstream |
| GitHub About/topics | repo settings via `gh repo edit` | About blurb and topics that match the target queries |

## Target queries

- compound engineering claude code plugin
- slim compound engineering fork
- claude code code review agents
- lightweight compound engineering plugin
- convert claude code plugin to codex

## Probe procedure

Probes are read-only. They observe how the repo surfaces; they do not change any owned surface.

1. Run each target query on a general web search engine (for example a standard search engine result page).
2. Run each target query on at least one AI answer engine (for example an LLM-backed search or answer product).
3. For each run, record whether `OutlineDriven/compound-engineering-slim` surfaces, whether the upstream `EveryInc/compound-engineering-plugin` surfaces, or whether neither does.
4. Note the position (rank on the results page) or the citation (whether the answer engine linked or named the repo).
5. Append one row per query/engine pair to the Baseline table below.

## Baseline

| date | query | engine | result | notes |
| --- | --- | --- | --- | --- |

## Iteration cadence

Re-probe and refresh the owned surfaces on notable inventory changes (skills, agents, or converter targets added or removed) or on a release. Whenever the slim-vs-upstream comparison table is touched, re-check the dated upstream snapshot (39 skills / 43 agents, June 2026) against the current upstream repo so the comparison stays accurate and the snapshot date is updated.
