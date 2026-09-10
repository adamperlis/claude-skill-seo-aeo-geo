# seo-aeo-geo — a Claude skill for SEO / AEO / GEO

A [Claude Code](https://claude.com/claude-code) skill for auditing and implementing **SEO**,
**AEO** (Answer Engine Optimization), and **GEO** (Generative Engine Optimization) on
websites and Ghost themes — so your content shows up in Google AI Overviews and gets cited by
ChatGPT, Perplexity, Gemini, and Claude.

It ships with a mental model for how AI search actually chooses sources, an engine-by-engine
playbook (they conflict — the skill says so), the FSA framework (Freshness / Structure /
Authority), a run-in-order audit framework, copy-paste JSON-LD schema patterns for Ghost, and
a full audit checklist.

## What makes it stay current

The skill's **first step** fetches live, weekly-refreshed research from a companion repo —
[`aeo-geo-research`](https://github.com/adamperlis/aeo-geo-research) — via a simple HTTP fetch
of raw Markdown. No API key, no MCP, no account. If the fetch fails, it falls back to the
snapshot bundled in `references/feed-insights.md`, and the durable principles in `SKILL.md`
always apply regardless.

That companion repo is auto-published every week by a distiller that summarizes fresh public
AEO/SEO/GEO sources (vendor studies, official docs) into cited, trust-labeled insights — so
the skill's recommendations keep pace with a fast-moving field without you editing anything.

## Install

**Easiest — as a plugin, right inside Claude Code (no terminal, auto-updates):**

```
/plugin marketplace add adamperlis/claude-skill-seo-aeo-geo
/plugin install seo-aeo-geo@adamperlis
```

Then start a new session. To pull future improvements: `/plugin update seo-aeo-geo`.

**Alternative — clone into your skills folder (plain git, updatable with `git pull`):**

```bash
git clone https://github.com/adamperlis/claude-skill-seo-aeo-geo ~/.claude/skills/seo-aeo-geo
```

Either way, the skill triggers automatically on SEO/AEO/GEO topics, or invoke it with
`/seo-aeo-geo`. (Claude Code only needs `SKILL.md` + `references/`; the other files are
harmless extras.)

## Use

Just ask — the skill triggers on SEO/AEO/GEO topics automatically, or invoke it explicitly:

```
/seo-aeo-geo audit my Ghost theme for AI visibility
```

## Optional: ground it in your own research vault

If you keep a private notes/research vault exposed to Claude via an MCP (e.g. Obsidian), the
skill will *also* search it for deeper, private grounding — see Step 2 of `SKILL.md`. This is
entirely optional; the skill works fully without it.

## Files

| File | What it is |
|---|---|
| `SKILL.md` | The skill itself — mental model, engine map, FSA, audit framework, reporting |
| `references/schema-patterns.md` | Ready-to-use JSON-LD blocks for Ghost/Handlebars themes |
| `references/audit-checklist.md` | Systematic full SEO/AEO/GEO audit checklist |
| `references/feed-insights.md` | Offline snapshot of the distilled insights (live copy is fetched at runtime) |

## License

MIT © Adam Perlis. See [LICENSE](LICENSE).
