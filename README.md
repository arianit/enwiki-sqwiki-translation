# enwiki → sqwiki translation

An AI assistant skill for translating English Wikipedia articles into Albanian as paste-ready wikitext for [sq.wikipedia](https://sq.wikipedia.org).

https://github.com/arianit/enwiki-sqwiki-translation

## What it does

Given the wikitext of an enwiki article, it produces a `.wiki` file an experienced sqwiki editor can paste with minimal cleanup, plus an explicit report of what could not be verified. It handles:

- **Title translation** — meaning over word order, with the genitive direction right, checked against how existing sqwiki prose already names the topic
- **Link and template verification** — Wikidata sitelinks for templates and articles; a cache of already-confirmed targets in `references/sqwiki-verified.md`
- **Source languages** — `|language=` on every citation, since on sqwiki even English sources are foreign-language
- **ISO dates**, Albanian number formatting (`3.852.242`, `0,2%`), Albanian section headings and categories
- **Dual transliteration** — Albanian form with the original in parentheses on first mention, for Slavic and non-Latin names
- **Safe substitutions** for enwiki templates with no confirmed sqwiki counterpart

## Install

**Claude Code** — personal scope:

```bash
git clone https://github.com/USERNAME/enwiki-sqwiki-translation.git
ln -s "$PWD/enwiki-sqwiki-translation/enwiki-sqwiki-translation" ~/.claude/skills/
```

Or copy the inner folder into `~/.claude/skills/` directly. If `~/.claude/skills/` did not exist before, restart the assistant so the directory gets watched. Confirm with `/skills`.

## Repo layout

```
enwiki-sqwiki-translation/       the skill itself — this folder is what gets installed
  SKILL.md                       instructions
  references/
    sqwiki-verified.md           cache of confirmed sqwiki template and article names
README.md
LICENSE
```

## Environment note

Verification is much more efficient when the assistant can reach Wikimedia hosts directly. With `en.wikipedia.org` and `sq.wikipedia.org` reachable:

```bash
curl -s "https://en.wikipedia.org/w/index.php?title=PAGE&action=raw"        # source, no paste needed
curl -s "https://sq.wikipedia.org/w/api.php?action=query&format=json&titles=A|B|C"   # 50 link checks per call
```

When network access is restricted, fall back to the cached results in `references/sqwiki-verified.md` and one-at-a-time web searches.

## Status

Working draft. Exercised on four articles of varying size and citation style — a short battle article, a mid-length historical campaign, a heavily-cited medical article, and a very long philosophy article — and revised after each. Not benchmarked, and the description has not been tuned for triggering accuracy.

The verified-targets cache will drift as sqwiki changes. If an entry fails, re-verify and correct it in place rather than working around it.

## Contributing

Corrections to the Albanian terminology and to the verified-targets table are the most useful contributions. Please note in the commit message how you confirmed a target (Wikidata item, search result URL, or direct check on sqwiki).

## Licence

CC BY-SA 4.0 — see `LICENSE`.

Note that this licence covers the skill instructions only. Article translations produced with it are derivative works of CC BY-SA Wikipedia content and must be attributed accordingly: credit `en:Article name` and the source oldid in the sqwiki edit summary.
