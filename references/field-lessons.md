# Field Lessons (first-party, observed)

> **What this file is:** lessons from audits run with this skill on real sites — what agents
> actually did, not what a vendor study says they do. Each entry is dated and names its evidence.
>
> **Trust level:** high for *what happened*, low for *how often*. Most entries come from one
> signed-out run per agent on one day; answers vary between runs. Read them as directional, and
> re-test before quoting a rate. When an entry conflicts with a large study in SKILL.md, prefer
> the study for rates and this file for mechanics (what was fetched, cited, blocked).
>
> Hand-edited. `feed-insights.md` is the auto-maintained file; do not put these there.

---

## The diagnostic rule: split branded from unbranded

**2026-10-22 — a desktop dictation app, ChatGPT + Perplexity + Brave Ask, signed out.**
Asked about the app by name, the agents got every fact right: 7 of 7 answers (prices, plan limits,
"HIPAA-ready, not certified, no BAA", "no bot joins the meeting"). Asked "which dictation app should
I use", they never named it: 0 of 8 answers.

- **Branded right, unbranded absent → off-site problem.** The site is legible; nobody else is
  talking about the brand. Schema, `llms.txt` and page structure will not move the unbranded number.
  Say so plainly before recommending on-site work.
- **Branded wrong → on-site problem first.** Fix facts, structure and fetchability before chasing
  mentions — more mentions of a wrongly-described brand spread the wrong description.
- **Branded answers came from help-center docs**, not the marketing homepage: a plain-text plans
  page and topic pages (healthcare, meetings) were cited. A plain, current
  docs page per fact cluster (pricing, platforms, privacy) is what agents read.
- Perplexity labelled the privacy claims "company-stated, not audited". With no third-party source,
  even correct facts are reported with a hedge.

## Running the agent test

**2026-10-22.** The test that produced the rule above:

1. **Questions:** real buying questions, from the support inbox first. If there is none, model them
   on the site's own "questions people ask" list and the target buyers. Write both kinds:
   - *branded* ("is X free, what's the catch, what do plans cost?")
   - *unbranded* ("cheaper alternative to <leader> for Mac and Windows, with prices")
   Include one in each market language the brand sells in.
2. **Agents:** at least three, from different companies. Record for every answer: was the brand
   named, were its facts right, **which sources were cited**, and **which competitors were named**.
   The last two are the roadmap (see the lessons below).
3. **Rates:** one run per agent is directional. Repeat runs before calling anything a rate.

**Access, as of 2026-10:**

| Agent | Signed out? |
|---|---|
| ChatGPT (web search) | Works |
| Brave Ask | Works |
| Perplexity | One question, then "sign up and repeat your request" |
| Google AI Mode | CAPTCHA — stop; never try to get past it |
| Microsoft Copilot | Needs sign-in |
| Meta AI | Needs its AI Terms accepted — that is the user's call, not yours |

**CLI agents** can run the panel unattended, but each needs working auth:
- Claude Code: `claude -p "<question>"` from an **empty directory**, with
  `--strict-mcp-config --mcp-config '{"mcpServers":{}}'`, so the user's own CLAUDE.md, skills and
  memory don't leak into the answer (they often mention the brand). `--bare` also skips them but
  only works with an API key. A neutral `--system-prompt` and `--allowedTools WebSearch,WebFetch`.
- Codex: `codex --search exec --skip-git-repo-check -s read-only -C <empty dir> "<question>"`.
- Gemini CLI: `gemini -p "<question>" --approval-mode plan`.
- In the 2026-10-22 run all three failed on auth (expired login, model not on plan, personal
  sign-in retired). Check auth with one throwaway question before scheduling a panel.

## Fetchability: test as the agents, not as a browser

**2026-10-22.** Fetch the key pages with each agent's user agent and compare:

```bash
for ua in "ChatGPT-User/1.0" "Claude-User/1.0" "Perplexity-User/1.0" "PerplexityBot/1.0" \
          "GPTBot/1.2" "meta-externalfetcher/1.1" "Googlebot/2.1"; do
  for p in / /pricing /download /llms.txt; do
    printf "%-22s %-10s %s\n" "$ua" "$p" \
      "$(curl -s -o /dev/null -L -w '%{http_code} %{size_download}' -A "Mozilla/5.0 (compatible; $ua)" "https://SITE$p")"
  done
done
```

- **Status codes** catch blocking. **Byte sizes** catch fake pages: `/pricing` and `/download`
  returned exactly the homepage's size because both redirect to a homepage anchor. An agent asked
  "what does X cost" then gets no page whose subject is pricing.
- **Read the no-JS HTML** for the facts. Prices split across spans, or only the annual price
  rendered behind a monthly/annual toggle, reach an agent incomplete.
- **robots.txt is not the only gate.** A firewall's bot protection challenges machine fetches that
  robots.txt allows. One audited site's Vercel bot protection once answered Apple's Universal Links fetch with
  a 429 challenge. Check the firewall/WAF rules, not just the file.

## robots.txt: training crawlers vs the customer's assistant

**2026-10-22.** Two kinds of AI bot, with opposite stakes:

| Kind | Names | Blocking it means |
|---|---|---|
| Training / indexing crawler | `GPTBot`, `ClaudeBot`, `meta-externalagent`, `Google-Extended` (a control token, not a crawler) | Your content stays out of training; a legitimate choice |
| User-initiated fetcher | `ChatGPT-User`, `Claude-User`, `Perplexity-User`, `meta-externalfetcher` | A person asked their assistant to read your page and you refused — a lost customer |

- Audit them separately. One audited site blocked `ChatGPT-User` and `PerplexityBot` from everything except two
  paths, so people's assistants couldn't read its job listings, which already carried `JobPosting`
  markup.
- **Stale names do nothing.** `Claude-Web` and `AnthropicAI` are old; a rule for them neither blocks
  nor allows today's Claude fetcher. Check names against each vendor's current bot documentation.

## Comparison pages: cover the competitors agents name

**2026-10-22.** The audited app had comparison pages for the category leader, the operating
system's built-in dictation and several adjacent tools. The unbranded answers named six other
dictation apps — one of them in nearly every answer — and none had a page.

- Build the comparison/alternatives list **from the agent test's named competitors**, not from the
  brand's own idea of its rivals.
- Agents fan out to "<leader> alternatives" and "<A> vs <B>". A page for those exact phrasings is
  only useful if it **ranks in classic search** — see the next lesson.
- Keep comparisons honest (a real "where they win" section, dated and sourced prices). A
  comparison that reads as an ad is the kind of page agents learn to discount.

## Who wins unbranded answers in niche software

**2026-10-22.** The sources behind the 8 unbranded answers:
- **Rival-written "best X" roundups** (competitors' own blogs) were the single biggest source;
  ChatGPT's main source for one answer was a competitor's comparison article.
- **Reddit threads** in the category's subreddits (Brave especially).
- **YouTube comparison videos**, including one in the market language for the Spanish question.
- **Directory/editorial listings:** Product Hunt, Zapier, PCMag, TechCrunch.

The brand's own "best local dictation apps" post and its comparison page against the category leader were indexable and
in the sitemap, but not on page one of classic search for those queries, so no agent used them.
**A brand-owned listicle is only cited if it ranks.** Check page-one results for the category
queries before recommending another one.

Off-site moves that follow: ask to be added to the roundups that are cited; answer the existing
Reddit threads openly as the maker, within each subreddit's rules; reach the YouTube comparison
creators; get listed where those directory pages draw from.

## Generic brand names collide

**2026-10-22.** The audited app's name is also the common word for an unrelated product. Brave's
supporting links under correct answers about the app were about the unrelated product instead.

- In every third-party mention, pair the name with the category: "Acme dictation",
  "Acme voice-to-text", never "Acme" alone.
- Check that each `sameAs` points at **this** entity. The audited app's pointed at the parent company's
  Product Hunt product, which merges two entities.
- Check that `sameAs` is complete: every app store listing, social profile and directory page.

## Read the reason before "fixing" schema

**2026-10-22.** The audited app's product entity was a generic `Thing`. That looks like a gap, but a code
comment explained it: Google's SoftwareApplication rich result requires a real `aggregateRating` or
`review`, and the brand publishes neither, so `SoftwareApplication` would show as an invalid item.

- Look for a comment, commit message or decision note before recommending a schema type change.
- What *can* still be fixed inside a deliberate type: a stale `description`. Its description
  listed three platforms after two more had shipped.

## Facts drift between copies

**2026-10-22.** The audited app kept canonical product facts in one data module, but the homepage FAQ
structured data and the product description were typed separately. Both had gone stale: the FAQ's
platforms answer left out two platforms, and one FAQ promised a feature the price card marked
"Soon" (Perplexity noticed).

- Generate FAQ JSON-LD, product descriptions and `llms.txt` facts **from the same source** as the
  visible page.
- In an audit, diff every machine-readable copy of a fact (JSON-LD, `llms.txt`, docs, app store
  listing) against the canonical one. Agents read the machine copy first.
