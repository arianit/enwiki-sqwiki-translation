---
name: enwiki-sqwiki-translation
version: beta1
description: Translate English Wikipedia articles into Albanian as paste-ready wikitext for sq.wikipedia, with source languages marked in every citation, ISO dates, Albanian number formatting, and dual transliteration of Slavic and non-Latin names. Use this skill whenever the user asks to translate a Wikipedia article, mentions sqwiki / sq.wiki / Albanian Wikipedia, pastes wikitext or a Wikipedia URL and asks for it "in Albanian", or asks to check redlinks, template existence, or citation language parameters in an Albanian article — even if they only say "translate this" and the source is obviously a Wikipedia article.
---

# enwiki → sqwiki translation

Produce a complete Albanian wikitext file that an experienced sq.wikipedia editor can paste with minimal cleanup, plus an honest report of what could not be verified.

The deliverable is a `.wiki` file in the outputs directory, not chat prose. Wikitext in a chat bubble is awkward to copy and loses formatting.

## Get the source right first

**Always fetch wikitext via `?action=raw`, never the rendered page.** A rendered enwiki article returns prose: citation templates collapse, ref names vanish, and reconstructing ~180 `{{cite}}` templates by hand is both enormous and error-prone.

If the user gives a URL or article title, fetch the raw wikitext directly:

```
https://en.wikipedia.org/wiki/ARTICLE_NAME?action=raw
```

For example, `https://en.wikipedia.org/wiki/Siege_of_Shkodra?action=raw`. If they already pasted wikitext, work from that instead.

If the raw fetch fails and you have to fall back to the rendered page — fetch the normal article URL and parse what you get — it is workable but degraded, and you must say so. Citation parameters that don't render (`|url-status=`, `|via=`, `|display-authors=`) are invisible, short-form `{{sfn}}` citations arrive as bare "Author Year, p. N" that you are reconstructing by inference, and the infobox arrives as a flattened list where you can't see which parameter held what. Flag reconstructed citations as needing a check against the source.

**Check whether the sq article already exists.** Fastest route is the Wikidata item: its sitelink list names the sqwiki article if there is one, and its absence is equally informative. This works for articles exactly as it does for templates, in one search. Fall back to a web search for the likely Albanian title. If the article exists, this is a rewrite, not a new page — say so, because it changes how the user approaches saving, and the existing article may have a usable infobox or categories worth keeping.

**Attribution is required.** sqwiki content is CC BY-SA; a translation is a derivative work. Tell the user to credit `en:Article name` and the oldid in the edit summary (or use sqwiki's translation template). Mention this once, at the end.

## Title

Translate the *meaning*, not the word order. Get the direction of genitives right — Albanian marks the agent and object with case, and a mechanical translation frequently reverses them.

Do **not** trust an `{{langx|sq|…}}` gloss inside the enwiki article. These are often added by non-speakers and are a common source of exactly this error. Verify the title independently and, if the enwiki gloss is wrong, tell the user so they can fix it upstream.

Example of the failure mode: for "Scutari invasion of Montenegro", `Pushtimi i Shkodrës në Mal të Zi` parses as "the conquest *of Shkodra*, located in Montenegro" — the reverse of the meaning. `Pushtimi i Malit të Zi nga Pashallëku i Shkodrës` is correct.

**Best check available: find the topic named in existing sqwiki prose.** Related sq articles often already refer to your subject in passing, and that gives you the community's own wording rather than your invention. Searching for the topic alongside a likely Albanian keyword surfaces these. It confirmed *Beteja e Krusit* — sqwiki's article on the Brda region already called it that — and it is how you discover house conventions for names before committing to your own transliterations. Do this before translating the title from scratch, not after.

The enwiki gloss is sometimes right; the point is not to assume either way. When it's right, say so, so the user isn't left wondering whether you checked.

## Verify link targets and templates

Assume nothing about what exists on sqwiki. Guessed titles produce redlinks that look authoritative and get copied around.

**Templates: check Wikidata sitelinks.** Search `wikidata "Template:X" sqwiki` — the sitelink list on the Wikidata item names the sqwiki equivalent (`Stampa:…`) if there is one. This is fast and definitive. `references/sqwiki-verified.md` holds results already confirmed this way; read it before searching, and add to it as you verify more.

**Articles: search for the Albanian title.** The result URL confirms both existence and exact title, including surprises like `Çetina` vs `Cetina`.

**sqwiki sometimes translates the template name, sometimes not.** `Stampa:Infobox military conflict` and `Stampa:Infobox medical condition` keep the English name, but the philosopher infobox is `Stampa:Infobox filozof`. There is no rule — check each one. Never assume the enwiki name carries over just because a sibling template did.

**Verifying the template name is not the same as verifying its parameters.** A localized template may also localize its parameter names, in which case English parameters render blank and the infobox silently loses half its content. When the name is translated, treat the parameters as unverified too and say so.

**Substitutions that always work.** When a template can't be confirmed, prefer plain MediaWiki over a guess — it cannot break:

| enwiki | safe substitute |
|---|---|
| `{{efn-ua}}` / `{{notelist-ua}}` | `<ref group="sh">…</ref>` + `{{Reflist|group=sh}}` |
| `{{blockquote}}` | `<blockquote>…</blockquote>` |
| `{{small}}` | `<small>…</small>` |
| `{{Transliteration|grc|x}}` | `''x''` |
| `{{langx|grc|Χ|Y}}` | `(greqishtja e vjetër: Χ, ''Y'')` |
| `{{font color}}` in tables | the colour word in plain Albanian |
| `{{cite SEP}}`, `{{cite IEP}}`, `{{NINDS}}` | plain `cite web` with the real URL |
| bundled `{{harvnb}}` inside one `<ref>` | consecutive `{{Sfn}}` calls |

The harvnb→Sfn swap has a visible cost: one footnote holding six sources becomes six footnotes, inflating the citation count. `Stampa:Harvnb` probably exists wherever `Stampa:Sfn` does — verify it and keep the bundles if you can.

**When you can't verify, don't guess.** For a short article, verify every link. For a long one, verification of 150 links is not feasible: link only titles you are confident about, leave the rest as plain text rather than seeding redlinks, and list what you left unlinked. Prefer under-linking to a page full of red.

Also drop enwiki project furniture that has no sqwiki counterpart: `{{Short description}}`, `{{Use dmy dates}}`, `{{cs1 config}}`, `{{TOC limit}}`, `{{About}}`, `{{Distinguish}}`, maintenance tags, navboxes, `{{Authority control}}`, `{{Portal bar}}`. Expand any template that only wraps a citation (e.g. `{{NINDS}}`) into a plain `cite web`.

## Citations

**Mark the language of every source** with `|language=` and an ISO code — `en`, `de`, `sr`, `sq`, `pl`, `pt`, `vi`. On sqwiki even English sources are foreign-language, so `en` is not optional. CS1 renders the localized name if the module is localized and the English name otherwise; either way the information is there. Check the actual language of each source rather than copying enwiki's parameter — a blog post with an Albanian title is `sq` even if enwiki tagged it `en`. Add `trans-title` for non-English titles, translated into Albanian.

**Dates: ISO.** `YYYY-MM-DD` for full dates, `YYYY-MM` for month-only. Albanian month names inside `|date=` may trip sqwiki's date validation, and ISO is unambiguous. Scope any bulk conversion to `|date=`, `|archive-date=` and `|access-date=` so prose dates stay in Albanian — a regex loose enough to hit running text will quietly corrupt the article.

**Archive links.** Keeping every `archive-url`/`archive-date` pair roughly doubles the file size of a heavily-cited article. InternetArchiveBot runs on sqwiki and re-adds them, so stripping them is defensible for long articles — but say you did it and offer to restore them.

**Quotes inside `|quote=`.** Keep them short. Long block quotes from in-copyright books are a problem regardless of which Wikipedia they sit on; trim to a brief excerpt or drop the parameter. Public-domain-era sources are fine.

**Unlink institution names inside refs** (`[[Centers for Disease Control and Prevention]]` → plain text) — those are enwiki titles and will redlink.

**Short-form citation articles.** Many history articles cite with `{{sfn}}` pointing at a `{{citation}}` bibliography rather than full inline refs. There `|language=` belongs on the bibliography entries, not at the `{{sfn}}` call sites — a per-ref sweep will find nothing to tag and miss the whole apparatus. `{{Sfn}}`, `{{SfnRef}}` and `{{citation}}` are all present on sqwiki, and sfn anchors resolve against `{{citation}}` automatically, so the pairing carries over unchanged.

Don't assume the source language distribution. A medical article may be almost entirely English; a Balkan history article may be almost entirely Serbian with one Albanian source and no English at all. Tag what is actually there.

## Names

Give both forms on first mention: the Albanian transliteration, with the original Latin or romanized form in parentheses — *Jovan Radoniqi (Jovan Radonjić)*, *Petri I Petroviq-Njegoshi (Petar I Petrović-Njegoš)*, *Pashtroviqët (Paštrovići)*, *Muhamed ibn Zekeria el-Raziu (Rhazes)*. Thereafter use the Albanian form alone. Western Latin-script names that Albanian doesn't reshape stay as they are.

When an sqwiki article exists for the person, link to its actual title even when that differs from your transliteration.

## Albanian conventions

- Numbers: `.` for thousands, `,` for decimals — `3.852.242`, `30.000–45.000`, `0,2%`.
- Section headings translated, not transliterated: `== Referime ==`, `== Burimet ==`, `== Lidhje të jashtme ==`, `== Historia ==`, `== Shoqëria dhe kultura ==`.
- Categories in Albanian with `[[Kategoria:…]]`. Flag any category name you invented.
- Technical vocabulary: pick one rendering per concept and hold it across the article (e.g. *imuniteti kolektiv* for herd immunity, *amnezia imunitare* for immune amnesia).

## Editorial judgement

Faithful translation is the default, but enwiki articles are often written for a US or UK reader. Where an article gives five paragraphs to American outbreaks and one sentence to an epidemic that killed thousands elsewhere, translate it faithfully and then flag the imbalance — trimming is the user's call, not yours. Point out where Albania- or Kosovo-relevant material exists in the source and deserves expansion from local sources.

Don't silently "fix" the source. If enwiki has a vague antecedent ("Later that day…" with no established day), preserve the vagueness and note it rather than inventing a date.

## Working method on long articles

**Acknowledge a large paste before doing anything else.** A one-line "got it, ~120 KB, starting now" costs nothing and makes a failed turn obvious immediately. A turn that consumes a huge paste and returns nothing forces the user to paste it again — which has happened, twice, and is pure waste.

**If the request bundles several tasks, say which one you are doing first.** Silently picking one and dropping the rest loses the others.

**Build very long articles in two files and concatenate.** Body through `== Referime ==` in one, bibliography and external links in the other, then `cat` them together. A single enormous write risks truncation, and splitting at the bibliography boundary makes the citation apparatus reviewable on its own.

**Audit the assembled file before presenting it.** Cheap checks that catch real errors:

```
grep -o 'language=[a-z, ]*' FILE | sort | uniq -c        # language spread
grep -o '{{Sfn' FILE | wc -l ; grep -c '^\* {{cite' FILE  # sfn calls vs bibliography entries
grep -o '{{\(langx\|efn-ua\|harvnb\|cite SEP\|font color\)' FILE | sort -u   # leftover enwiki templates
```

Report the counts. They are the evidence that the citation apparatus survived.

**High-traffic and quality-rated articles need a heads-up.** Replacing an existing sq article with a 100 KB translation is a big, visible edit. Suggest a talk-page note first.

## Report at the end

Keep the summary short and specific. Cover:

- Link targets corrected, as a before/after table if there are several
- What was verified as existing, and what you could not verify
- Templates confirmed present, and any left in that might be missing
- The language-code breakdown across citations
- Choices the user should review: stripped archives, dropped templates, pruned links, condensed sections
- The attribution reminder

Say plainly what you did not check. An unflagged unverified link is worse than an admitted one.
