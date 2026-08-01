---
name: enwiki-sqwiki-translation
version: beta6
description: Translate English Wikipedia articles into Albanian as paste-ready wikitext for sq.wikipedia, with source languages marked in every citation, ISO dates, Albanian number formatting, and dual transliteration of Slavic and non-Latin names. Use this skill whenever the user asks to translate a Wikipedia article, mentions sqwiki / sq.wiki / Albanian Wikipedia, pastes wikitext or a Wikipedia URL and asks for it "in Albanian", or asks to check redlinks, template existence, or citation language parameters in an Albanian article — even if they only say "translate this" and the source is obviously a Wikipedia article.
---

# enwiki → sqwiki translation

Produce a complete Albanian wikitext file that an experienced sq.wikipedia editor can paste with minimal cleanup, plus an honest report of what could not be verified.

The deliverable is a `.wiki` file in the outputs directory, not chat prose. Wikitext in a chat bubble is awkward to copy and loses formatting.

## Get the source right first

**Never fetch the rendered page.** A rendered enwiki article returns prose: citation templates collapse, ref names vanish, and reconstructing ~180 `{{cite}}` templates by hand is both enormous and error-prone.

**First option: the Action API**, `action=query` with `prop=revisions`. It returns the raw wikitext *and* the current `revid` in a single call — and the `revid` is exactly what's needed later for the attribution note, so this saves a second fetch:

```
curl -s -A "your-tool-name/1.0 (contact-info)" "https://en.wikipedia.org/w/api.php?action=query&prop=revisions&titles=ARTICLE_NAME&rvprop=content%7Cids&rvslots=main&formatversion=2&format=json" \
  | jq -r '.query.pages[0].revisions[0].slots.main.content' > ARTICLE_enwiki_raw.wiki
```

Pull `.query.pages[0].revisions[0].revid` out of the same response and hold onto it for the attribution reminder at the end. `titles=` must be URL-encoded (spaces as `%20` or `_`).

**Fallback: `?action=raw`**, if the API call fails or `jq` isn't available. It returns the wikitext directly as plain text — no JSON to unwrap — but does not give you the revid, so you'd need a separate call (`action=query&prop=revisions&rvprop=ids`) to get one for attribution:

```
curl -s "https://en.wikipedia.org/wiki/ARTICLE_NAME?action=raw"
```

Either way, save the result to a local file (`ARTICLE_enwiki_raw.wiki`) before translating. This avoids re-fetching and gives you a reference to diff against.

If both raw-wikitext routes fail and you have to fall back to the rendered page — fetch the normal article URL and parse what you get — it is workable but degraded, and you must say so. Citation parameters that don't render (`|url-status=`, `|via=`, `|display-authors=`) are invisible, short-form `{{sfn}}` citations arrive as bare "Author Year, p. N" that you are reconstructing by inference, and the infobox arrives as a flattened list where you can't see which parameter held what. Flag reconstructed citations as needing a check against the source.

**Check whether the sq article already exists.** Fastest route is the Wikidata item: its sitelink list names the sqwiki article if there is one, and its absence is equally informative. This works for articles exactly as it does for templates, in one search. Fall back to a web search for the likely Albanian title. If the article exists, this is a rewrite, not a new page — say so, because it changes how the user approaches saving, and the existing article may have a usable infobox or categories worth keeping.

If checking several titles against Wikidata, batch them into one `wbgetentities?sites=enwiki&titles=A|B|C` call rather than looping one request per title — looping hits rate limits after roughly 15–20 rapid requests, and the resulting empty/non-JSON response can look like a legitimate "no sitelink" result to a naive parser, wrongly reporting real articles as missing. See `references/sqwiki-verified.md` for the batching pattern.

**A rewrite target's existing content is not pre-verified.** An infobox template and its parameter names can be trusted if they are visibly rendering correctly on the live page — that's proof the template and params work. But individual wikilinks *inside* that existing article are not proof of anything; treat them exactly like your own draft's links and verify them. A live article can render fine while linking to several redlinks.

**Attribution is required.** sqwiki content is CC BY-SA; a translation is a derivative work. Give the user both a ready-to-paste talk-page template and an edit-summary sentence — don't just describe the requirement and leave them to build it. Mention this once, at the end.

**`{{Përkthyer nga|LANG|ARTICLE|DATE|REVID}}` goes on the Talk page, never in the article.** Its `{{#if:{{NAMESPACE}}|…}}` check means it renders a self-correcting error ("gabimisht është përfshirë në artikull — vendose në faqen e diskutimit") when placed in article space, because `{{NAMESPACE}}` is the empty string there; it only displays correctly on Talk or any other non-main namespace. Confirmed 2026-08-02 by fetching the template source — don't place it at the top of the article body.

**End every output file with a trailing, HTML-comment-wrapped block**, so it stays inert if the whole file is pasted straight into the article, holding both ready-to-copy pieces:

```
<!--
NUK ËSHTË PJESË E ARTIKULLIT — mos e kopjo këtë bllok në faqen kryesore.

== Për faqen e diskutimit (Talk) ==
{{Përkthyer nga|en|ARTICLE_NAME|DD MUAJ YYYY|REVID}}

== Përmbledhja e redaktimit ==
Përkthyer nga anglishtja, sipas artikullit en:ARTICLE_NAME, versioni i datës DD MUAJ YYYY (revizioni REVID).
-->
```

The edit-summary line is the same facts as one prose sentence, for the "Edit summary" field when saving (which doesn't render templates) rather than the talk-page notice. Get the revision's timestamp via `action=query&prop=revisions&revids=REVID&rvprop=timestamp` if it wasn't already captured during the fetch — don't guess the date. Paste both pieces into the chat response too, not only the file, since the user may act on them before opening it.

**Talk-page template**, confirmed present as `Stampa:Përkthyer nga` (verified 2026-08-01) — paste it on the article's *talk* page (`Diskutim:Titulli i artikullit`), not the article itself; the template detects the wrong namespace and prints a warning if misplaced:

```
{{Përkthyer nga|en|EXACT_ENWIKI_TITLE|DATË_SHQIP|REVID}}
```

- `1=` the source-wiki language code (`en`)
- `2=` the exact enwiki title, spaces as spaces not underscores (e.g. `Archimedes`, not `List_of_equipment...`)
- `3=` the date of the source revision, in Albanian prose form (`28 korrik 2026`, not `2026-07-28` — this is a rendered sentence, not a citation date field, so it does not follow the ISO rule used elsewhere in this skill). Albanian month names: `janar, shkurt, mars, prill, maj, qershor, korrik, gusht, shtator, tetor, nëntor, dhjetor`.
- `4=` the `revid` captured during the initial fetch (see "Get the source right first")

Worked example from an actual job: `{{Përkthyer nga|en|Archimedes|28 korrik 2026|1366588187}}`.

**Edit-summary sentence** — same three facts (source language, source title, revision), just as prose instead of a template call, since edit summaries don't render wikitext:

```
Përkthyer nga anglishtja, artikulli «EXACT_ENWIKI_TITLE», versioni i datës DATË_SHQIP (rev. REVID).
```

Fill in both from the same `revid` and title you already have — there's no separate lookup.

## Title

Translate the *meaning*, not the word order. Get the direction of genitives right — Albanian marks the agent and object with case, and a mechanical translation frequently reverses them.

Do **not** trust an `{{langx|sq|…}}` gloss inside the enwiki article. These are often added by non-speakers and are a common source of exactly this error. Verify the title independently and, if the enwiki gloss is wrong, tell the user so they can fix it upstream.

Example of the failure mode: for "Scutari invasion of Montenegro", `Pushtimi i Shkodrës në Mal të Zi` parses as "the conquest *of Shkodra*, located in Montenegro" — the reverse of the meaning. `Pushtimi i Malit të Zi nga Pashallëku i Shkodrës` is correct.

**Best check available: find the topic named in existing sqwiki prose.** Related sq articles often already refer to your subject in passing, and that gives you the community's own wording rather than your invention. Searching for the topic alongside a likely Albanian keyword surfaces these. It confirmed *Beteja e Krusit* — sqwiki's article on the Brda region already called it that — and it is how you discover house conventions for names before committing to your own transliterations. Do this before translating the title from scratch, not after.

The enwiki gloss is sometimes right; the point is not to assume either way. When it's right, say so, so the user isn't left wondering whether you checked.

This same check — searching existing sqwiki prose before inventing a rendering — applies well beyond the title. See below.

## Check nearby sqwiki articles before inventing a phrase

The technique above for the title generalizes to any recurring term, concept, or proper name in the body: a related sqwiki article has often already solved the same translation problem, and its answer is better evidence than a fresh guess, because it's what readers and other articles already expect.

Before settling on a rendering for a specialized term, an institution, a recurring concept, or a named work, check whether sqwiki already has an article that uses it. Example: an article that references "the Socratic dialogues" should first be checked against sqwiki's own article on the topic (search `wikidata "Socratic dialogues" sqwiki` or a plain web search scoped to `site:sq.wikipedia.org`) — if sqwiki already renders it as, say, `Dialogjet sokratike`, use that wording everywhere the phrase recurs in your translation rather than retranslating it from scratch each time.

This matters most for:
- **Recurring named concepts** that appear more than once in the article (a named battle, a school of thought, a recurring institution) — inconsistent renderings across occurrences look worse than a single wrong one.
- **Terms shared with other articles on the same topic cluster** — if you're translating one Socratic-dialogue-adjacent article, sqwiki likely already has others, and they should agree with each other.
- **Names and terms you're least confident about** — when in doubt, this check is faster than reasoning about grammar from first principles, and it's exactly what the loanword-plural and vocabulary tables above are precedent for.

Confirm the context matches before borrowing a term — the same English word can map to different Albanian words depending on sense (see "Vocabulary choices" below), so check that the existing article uses it the same way you need it, not just that the word appears somewhere on sqwiki.

## Albanian grammar

These are recurring errors in machine-assisted translation that must be checked in every article.

### Wikilinks must carry inflected display text

Albanian is a heavily inflected language. When a linked phrase appears inside a sentence, the **display text must match the grammatical case required by the sentence**, even when the wikilink target is in nominative form.

Wrong: `Në [[Franca e Re]] të hershme` — "në" requires accusative, but display text is nominative.
Right: `Në [[Franca e Re|Francën e Re]] të hershme`

Common preposition → case mappings:
- `në` + definite noun → accusative: `Athinë` → `Athinën`, `Franca e Re` → `Francën e Re`
- `nga` + definite noun → ablative: `Athina` → `nga Athina` (often unchanged for cities)
- genitives/datives require agreement with the modified noun

Scan the whole article for wikilinks that appear after prepositions or as objects and verify the display text is inflected correctly. This is especially likely to be wrong in infobox values and image captions.

### Vocabulary choices for "accounts / sources"

The English word **"accounts"** (as in historical sources) must not be translated as `llogaritë` — that word means financial accounts or user login accounts. Choose contextually:

| English | Albanian | Use when |
|---|---|---|
| testimonies, evidence from sources | `dëshmi` | primary or documentary evidence ("accounts by ancient writers") |
| narratives, written accounts | `rrëfime` | narrative works (dialogues, memoirs, histories) |
| sources | `burime` | sources in general ("these ancient sources") |
| explanation, account of | `shpjegim` | an account/explanation of an inconsistency or event |
| version, account as portrayal | `portretizim` | how a source portrays someone |

### Abbreviations for era markers

Use `p.e.s.` and `e.s.` in prose and infoboxes — do **not** hyperlink and spell out `[[para erës sonë]]`. The article already uses the abbreviations in prose, so a linked full form in the infobox is inconsistent. In categories and category-like contexts, the full form `para erës sonë` is standard and should stay.

### Albanian plurals of loanwords

Loanwords ending in `-g` often palatalize in the plural. Verify before assuming the English plural suffix pattern applies:

| singular | indefinite pl. | definite pl. nom. | definite pl. gen. | confirmed |
|---|---|---|---|---|
| dialog | dialogje | dialogjet | dialogjeve | yes — sqwiki article `Dialogjet e Platonit` |
| blog | blogje | blogjet | blogjeve | by analogy |

Common error pattern: writing `dialogët` / `dialogëve` (treating it like a native Albanian noun) instead of `dialogjet` / `dialogjeve`.

## Verify link targets and templates

Assume nothing about what exists on sqwiki. Guessed titles produce redlinks that look authoritative and get copied around.

**Verify before drafting, not after — batch, don't loop.** The costly failure mode is translating optimistically (linking everything that looks plausible) and then auditing the finished draft to find and fix redlinks. That produces multiple correction passes: a first sweep catches the obvious misses, then a second catches case-mismatched or inflected forms that slipped through, then a third catches whatever the first two missed. Each pass is redundant tool round-trips. Do it once instead:

1. Before writing any prose, `grep -oE '\[\[[^]|#]+(\|[^]]+)?\]\]' SOURCE.wiki` to pull every wikilink target out of the source article.
2. Triage the list: skip one-off name-drops (a historian cited once, a publisher, an obscure crater) — these are never worth verifying, go straight to plain text for them. Keep recurring concepts, central proper nouns, and anything you're translating a guessed Albanian rendering for.
3. Batch-check the survivors against sqwiki's `action=query&titles=A|B|C` (up to 50 titles per call, `redirects=1`) — 2–3 calls covers most articles. For Wikidata sitelink checks specifically, batch the same way (`wbgetentities?sites=enwiki&titles=A|B|C`); looping one title per request hits rate limits after ~15–20 calls and a naive parser reads the resulting empty response as "no article," a false negative that silently unlinks real topics.
4. Only then draft, using the verified list to decide link vs. plain text per term.

**Past ~150 unique targets, do the batching in a throwaway script, not by hand.** A single history article rarely exceeds this, but an equipment list, order-of-battle, or filmography can easily surface 400–500+ unique link targets. Hand-crafting each `curl` call for a 10-batch job wastes turns and invites the exact one-at-a-time mistake this section already warns against, just at larger scale. Instead: extract all unique targets once (`grep -oE '\[\[[^]|#]+' SOURCE.wiki | sed -E 's/^\[\[//' | sort -u > targets.txt`), then run one small Python (or shell) loop that chunks the list into 50-title `wbgetentities` requests, pauses briefly between batches, and writes every result — found or not — to a `target<TAB>sqwiki_title_or_empty` TSV file. That file becomes a durable lookup table: the rest of the job is `grep` against it instead of re-hitting the API, and it survives if you need to revisit a section later in the same job.

**For anything the batch check doesn't confirm, default to an interwiki link, not plain text.** You already have the exact enwiki title — you extracted it from the source, so there is no guessing involved — so `[[:en:Exact Title|Display text]]` costs nothing to verify and can never produce a redlink. It also gives the reader a real path to information sqwiki doesn't cover yet, which plain unlinked text doesn't. This is a stronger default than "leave as plain text" specifically for articles dense with proper nouns unlikely to have individual sqwiki coverage — equipment models, unit designations, named systems, minor filmography entries — where sqwiki coverage will be sparse but the enwiki target is never in doubt. Reserve genuine plain text for terms that add nothing when linked (a generic adjective, a one-off descriptor with no article-worthy topic behind it).

**A "MISSING" Wikidata sitelink is not proof a small utility template doesn't exist.** Wikidata sitelinks only tell you two pages are *linked as the same item* — plenty of narrow-purpose formatting templates (`{{Nbsp}}`, `{{Anchor}}`-style one-liners) never get a Wikidata item connecting them across wikis at all, so a batched `wbgetentities` check reports them `MISSING` even when the sqwiki page exists and works fine. Before treating that as a real gap: check whether the template has a zero-cost native substitute (`&nbsp;` needs no template regardless of what `{{Nbsp}}` resolves to), and only spend a direct `?action=raw` fetch confirming/denying existence if there is no substitute and the template is actually going to be used repeatedly.

**Watch for false confidence from MediaWiki's case-folding.** MediaWiki only auto-capitalizes the *first character* of a title. `[[proteina]]` correctly resolves to `Proteina` — but an inflected or genitive Albanian form does not get the same courtesy: `[[kolerës]]` will NOT resolve just because `Kolera` exists, `[[tifos]]` will NOT resolve just because `Tifoja` exists. Check the literal bracketed string you're about to ship, not the lemma you have in your head. This is also why a redirect can trap you the other way: `[[virus]]` on sqwiki silently redirects to *Virusi kompjuterik* (computer virus), not the biological concept — a plausible-looking title resolving to the wrong topic is exactly the class of error batch verification exists to catch, and it will not show up as a redlink, so a "just check for red" review pass misses it entirely.

**Templates: check Wikidata sitelinks.** Search `wikidata "Template:X" sqwiki` — the sitelink list on the Wikidata item names the sqwiki equivalent (`Stampa:…`) if there is one. This is fast and definitive. `references/sqwiki-verified.md` holds results already confirmed this way; read it before searching, and add to it as you verify more.

**Articles: search for the Albanian title.** The result URL confirms both existence and exact title, including surprises like `Çetina` vs `Cetina`.

**sqwiki sometimes translates the template name, sometimes not.** `Stampa:Infobox military conflict` and `Stampa:Infobox medical condition` keep the English name, but the philosopher infobox is `Stampa:Infobox filozof`. There is no rule — check each one. Never assume the enwiki name carries over just because a sibling template did.

**Verifying the template name is not the same as verifying its parameters.** A localized template may also localize its parameter names, in which case English parameters render blank and the infobox silently loses half its content. When the name is translated, treat the parameters as unverified too.

**How to verify parameters for a translated infobox.** Fetch the template talk page or template page directly via curl:

```
curl -s "https://sq.wikipedia.org/wiki/Stampa:TEMPLATE_NAME?action=raw"
curl -s "https://sq.wikipedia.org/wiki/Stampa_diskutim:TEMPLATE_NAME?action=raw"
```

The talk page often has a documented parameter table. `references/sqwiki-verified.md` records confirmed parameter sets so you don't re-fetch on every job.

**Known case: `Stampa:Infobox filozof`** has a mix of Albanian and English parameters. English passthrough: `region`, `era`, `image`, `image_caption`, `signature`, `color`. Albanian: `emri`, `lindja`, `vendlindja`, `vdekja`, `vendvdekja`, `shkolla_tradita`, `interesimet`, `ndikimet`, `ndikoi_te`, `idetë`. Using the wrong names silently loses content.

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

The harvnb→Sfn swap has a visible cost: one footnote holding six sources becomes six footnotes, inflating the citation count. `Stampa:Harvnb` is now confirmed present (verified 2026-08-01) — you don't need to expand bundles, just verify it's still there.

**When you can't verify, don't guess.** For a short article, verify every link. For a long one, verification of 150 links is not feasible: link only titles you are confident about, leave the rest as plain text rather than seeding redlinks, and list what you left unlinked. Prefer under-linking to a page full of red. A Featured-Article-length biography can easily surface 200+ distinct wikilink targets; most of those are one-off citations of individual scholars, publishers, or minor named features — don't attempt to verify those at all, they're not worth a search. Reserve verification budget for names and concepts that recur, or that anchor a section.

Also drop enwiki project furniture that has no sqwiki counterpart: `{{Short description}}`, `{{Use dmy dates}}`, `{{cs1 config}}`, `{{TOC limit}}`, `{{About}}`, `{{Distinguish}}`, maintenance tags, `{{Authority control}}`, `{{Portal bar}}`. Expand any template that only wraps a citation (e.g. `{{NINDS}}`) into a plain `cite web`.

**Navboxes: drop enwiki-topic imports, but check for sqwiki-native curated lists first.** The blanket "drop navboxes" instinct is right for a navbox that mirrors an enwiki category (e.g. `{{Vaccines}}`, `{{Ancient Greek mathematics}}`) — there's rarely a sqwiki equivalent and it's not worth building one. But some sqwiki articles carry a *locally authored* navbox curating a topic cluster specific to that wiki (e.g. an "ancient and medieval Mediterranean authors" template linking dozens of related sq articles). If the subject you're translating already appears in such a template, keep it — it's real, working, sqwiki-native navigation, not an import. Tell the two apart by fetching the template's raw wikitext: an enwiki mirror will closely match the English navbox's structure and topic list; a native one won't.

## Images

**Include all inline images from the source.** A first-pass translation that omits `[[File:…]]` tags is incomplete and requires a second pass. Process images alongside the prose, not as an afterthought. Commons file names are cross-wiki and work on sqwiki without modification.

Translate the caption fully, including any embedded links (verify sqwiki link targets apply). Preserve `|thumb|`, `|left|`, `|right|`, `|upright=`, size hints as-is — they are layout instructions, not prose.

## Citations

**Mark the language of every source** with `|language=` and an ISO code — `en`, `de`, `sr`, `sq`, `pl`, `pt`, `vi`. On sqwiki even English sources are foreign-language, so `en` is not optional. CS1 renders the localized name if the module is localized and the English name otherwise; either way the information is there. Check the actual language of each source rather than copying enwiki's parameter — a blog post with an Albanian title is `sq` even if enwiki tagged it `en`. Add `trans-title` for non-English titles, translated into Albanian.

**Known CS1 Lua bug on sqwiki: `|language=grc` crashes.** sqwiki's Module:Citation/CS1 has `grc` (Ancient Greek) in `lang_tag_remap` but the corresponding `lang_name_remap` entry is broken or missing. Every cite template with `|language=grc` throws:

> Lua error te Moduli:Citation/CS1 te rreshti 1763: attempt to index field '?' (a nil value)

Fix: remove `|language=grc` from all cite templates. For Perseus or TLG links the Greek-language context is obvious. If you need to signal the language, add a parenthetical in the `|title=` or surrounding prose instead. Do not substitute `|language=el` (Modern Greek) — that is factually wrong.

**Dates: ISO.** `YYYY-MM-DD` for full dates, `YYYY-MM` for month-only. Albanian month names inside `|date=` may trip sqwiki's date validation, and ISO is unambiguous. Scope any bulk conversion to `|date=`, `|archive-date=` and `|access-date=` so prose dates stay in Albanian — a regex loose enough to hit running text will quietly corrupt the article.

**Archive links.** Keeping every `archive-url`/`archive-date` pair roughly doubles the file size of a heavily-cited article. InternetArchiveBot runs on sqwiki and re-adds them, so stripping them is defensible for long articles — but say you did it and offer to restore them.

**Quotes inside `|quote=`.** Keep them short. Long block quotes from in-copyright books are a problem regardless of which Wikipedia they sit on; trim to a brief excerpt or drop the parameter. Public-domain-era sources are fine.

**Unlink institution names inside refs** (`[[Centers for Disease Control and Prevention]]` → plain text) — those are enwiki titles and will redlink.

**Short-form citation articles.** Many history articles cite with `{{sfn}}` pointing at a `{{citation}}` bibliography rather than full inline refs. There `|language=` belongs on the bibliography entries, not at the `{{sfn}}` call sites — a per-ref sweep will find nothing to tag and miss the whole apparatus. `{{Sfn}}`, `{{SfnRef}}` and `{{citation}}` are all present on sqwiki, and sfn anchors resolve against `{{citation}}` automatically, so the pairing carries over unchanged.

**`{{Sfnmp}}` with two-author secondary citations.** When the second (or later) citation in an `{{Sfnmp}}` call has two authors, use `2last2=` for the second author's surname — do not duplicate the `2y=` parameter:

```
Wrong: {{Sfnmp|Foo|2020|p=1|2=Smith|2y=Jones|2y=2019|2p=5}}
                                          ^^^^^^^^^^^^^^ duplicate param; Jones is silently dropped
Right: {{Sfnmp|Foo|2020|p=1|2=Smith|2last2=Jones|2y=2019|2p=5}}
```

The wrong form causes the Sfnmp to look for `CITEREFSmith2019` but the cite book with `last1=Smith|last2=Jones` generates `CITEREFSmithJones2019`, so the footnote link breaks silently.

Don't assume the source language distribution. A medical article may be almost entirely English; a Balkan history article may be almost entirely Serbian with one Albanian source and no English at all. Tag what is actually there.

## Names

Give both forms on first mention: the Albanian transliteration, with the original Latin or romanized form in parentheses — *Jovan Radoniqi (Jovan Radonjić)*, *Petri I Petroviq-Njegoshi (Petar I Petrović-Njegoš)*, *Pashtroviqët (Paštrovići)*, *Muhamed ibn Zekeria el-Raziu (Rhazes)*. Thereafter use the Albanian form alone. Western Latin-script names that Albanian doesn't reshape stay as they are.

When an sqwiki article exists for the person, link to its actual title even when that differs from your transliteration.

## Albanian conventions

- Numbers: `.` for thousands, `,` for decimals — `3.852.242`, `30.000–45.000`, `0,2%`.
- Section headings translated, not transliterated: `== Referime ==`, `== Burimet ==`, `== Lidhje të jashtme ==`, `== Historia ==`, `== Shoqëria dhe kultura ==`.
- Categories in Albanian with `[[Kategoria:…]]`. Flag any category name you invented.
- Technical vocabulary: pick one rendering per concept and hold it across the article (e.g. *imuniteti kolektiv* for herd immunity, *amnezia imunitare* for immune amnesia).
- Era abbreviations: `p.e.s.` (para erës sonë = BC) and `e.s.` (erës sonë = AD) in prose and infoboxes. Do not hyperlink these or spell them out once the abbreviation has been introduced.

## Editorial judgement

Faithful translation is the default, but enwiki articles are often written for a US or UK reader. Where an article gives five paragraphs to American outbreaks and one sentence to an epidemic that killed thousands elsewhere, translate it faithfully and then flag the imbalance — trimming is the user's call, not yours. Point out where Albania- or Kosovo-relevant material exists in the source and deserves expansion from local sources.

Don't silently "fix" the source. If enwiki has a vague antecedent ("Later that day…" with no established day), preserve the vagueness and note it rather than inventing a date.

## Model efficiency

Not every step in this workflow needs the same amount of reasoning. Match effort to the step:

- **Mechanical steps — do directly, don't escalate.** Fetching wikitext, running a batched Wikidata-sitelink or article-titles check, a `grep` audit count, checking whether one template page exists: these are lookups, not judgment calls. Doing them as a heavier model or a spawned subagent is wasted cost for no better result. But "mechanical" doesn't mean "skip planning" — batch the lookups (see "Verify link targets and templates" above) rather than repeating the same category of check three times because the first pass wasn't comprehensive. An unplanned mechanical step repeated three times costs more than a planned one done once, even though each individual check is cheap.
- **Judgment steps — this is where a heavier model earns its cost.** Title direction and case (agent vs. object in a genitive), inflecting wikilink display text to match sentence case, disambiguating vocabulary (`llogaritë` vs. `dëshmi` vs. `burime`), reconciling terminology against a nearby sqwiki article's existing wording, spotting an `{{Sfnmp}}` parameter collision — these require actually reasoning about Albanian grammar and about what the source means, and are where mistakes are both likely and costly to unwind later.
- **If delegating a sub-task to a subagent** (e.g., a batch of link-existence checks across a long article), a lighter/faster model is normally sufficient — it's the same lookup work, just parallelized. Reserve a heavier model for a subagent only if you're asking it to make a translation or grammar judgment, not just verify a fact.

When in doubt about which bucket a step falls in: if getting it wrong would only mean re-running a search, it's mechanical; if getting it wrong would ship a grammatically or factually incorrect sentence into the article, it deserves the heavier pass.

## Working method on long articles

**Acknowledge a large paste before doing anything else.** A one-line "got it, ~120 KB, starting now" costs nothing and makes a failed turn obvious immediately. A turn that consumes a huge paste and returns nothing forces the user to paste it again — which has happened, twice, and is pure waste.

**If the request bundles several tasks, say which one you are doing first.** Silently picking one and dropping the rest loses the others.

**Build very long articles in two files and concatenate.** Body through `== Referime ==` in one, bibliography and external links in the other, then `cat` them together. A single enormous write risks truncation, and splitting at the bibliography boundary makes the citation apparatus reviewable on its own.

**The two-file split is a floor, not a ceiling — a long prose survey article needs more, even with no tables.** An article like "Sculpture" (168 KB, ~50 sub-sections spanning every world region and era, 28 galleries, no infoboxes or tables) doesn't fit the body/bibliography split cleanly: there is no single bibliography boundary and any one `Write` covering multiple major regions risks the same truncation the two-file rule exists to avoid. Split at natural content boundaries instead — by major topic block (e.g. one file for the lead through Materials, one per broad History era/region cluster, one for Modernism through the end) — and `cat` them in order at the end. Six part files of 100–150 lines each is a normal shape for an article this size; there's nothing wrong with more than two when the source has this much breadth. The test isn't "is it table-heavy," it's "does any single planned `Write` risk exceeding a safe size."

**Optional, exhaustive verification work is a follow-up, not part of the first pass.** A gallery-and-caption-dense survey article can name several hundred individual artists/artworks — batch-verifying all of them upfront is not a good use of the first pass; most readers won't need it, and guessing which subset is "key" wastes calls on names the user doesn't care about. Do the first draft with only the terms and recurring concepts already required by "Verify link targets and templates," report clearly that individual proper nouns were left unlinked and why, and offer to link key ones "once verified." If the user takes you up on it, that follow-up request itself is the filter: verify only the names that recur in continuous prose (not one-off gallery captions), batch them in one or two `wbgetentities` calls, and apply the confirmed subset. This produced a clean, small, high-value pass (13 artists, ~34 link instances) instead of a speculative several-hundred-title verification that would have mostly gone unused.

**Table-heavy "list of equipment" / order-of-battle articles are a distinct shape — plan for it before drafting a single row.** These run 300+ table rows across a dozen-plus sections with very little continuous prose: most of the work is short Type-column phrases and one-to-two-sentence Notes that repeat, near-verbatim, across dozens of rows, plus a flag/origin template on every row. Treat this differently from a prose article:

- Do the full link-extraction-and-batch-verify pass (see "Verify link targets and templates") *before* touching any table, not section by section. A Type phrase like "off-road vehicle" or "self-propelled howitzer" can recur 15–20+ times across the article; deciding its Albanian rendering once and reusing it from a lookup table is both faster and more consistent than re-deciding it fresh in each section, where it risks drifting to a different wording by row 200.
- Extend the two-file split to one file per top-level section (one per `==Heading==` in the source), and track each with `TaskCreate` — one task per section, marked complete as it's written, in source order. A 2,000-line, 16-section article is exactly the case the two-file split doesn't scale to; per-section files bound each write to a safe size and make the job checkpointed and resumable if it spans a long session.
- Flag/origin templates (`{{SRB}}`, `{{USSR}}`, `{{Flag|Czechoslovakia}}`, …) need the same existence check as any other template — batch them in one `action=query&titles=Stampa:SRB|Stampa:YUG|…` call, don't assume the whole family carries over uniformly. Most IOC-style codes do exist on sqwiki, but codes that are common on enwiki and rare elsewhere can be missing even when the underlying `Country data` page is present — `{{URS}}` (USSR) and `{{CZS}}` (Czechoslovakia) were both missing in one job while `{{USSR}}` and `{{flag|Czechoslovakia}}` worked. Check for a working alternative via `{{Flag|Country}}`/`{{Flagicon|Country}}` (these call `Stampa:Country data X` directly) before concluding the template needs to be dropped.
- A sibling article that only *partially* covers the same subject — a country's general military article summarizing a handful of items from the full list you're translating — is worth fetching in full before drafting. It can hand you the confirmed title, a working link convention for topics sqwiki hasn't covered (see the interwiki-fallback note above), and a batch of pre-vetted terminology in a single read. This is a bigger efficiency win here than the per-term nearby-article checks described in "Check nearby sqwiki articles" would suggest on their own — check for one even when the main topic doesn't have its own sqwiki article yet.

**Audit the assembled file with `grep`, not by `Read()`-ing it back in full.** You already have every part's content from writing it — re-reading a 150 KB+ concatenated file burns tens of thousands of tokens for no new information, and past a certain size it gets silently paginated/truncated by the read tool, forcing a second call just to see the rest. The grep commands below *are* the verification; they catch what a skim-read would miss anyway (a `Read()` of 700+ lines does not reliably surface a missing `|language=` tag or a leftover `{{cn}}`), so there's no accuracy tradeoff for skipping the full read. Cheap checks that catch real errors:

```
grep -o 'language=[a-z, ]*' FILE | sort | uniq -c        # language spread
grep -o '{{Sfn' FILE | wc -l ; grep -c '^\* {{cite' FILE  # sfn calls vs bibliography entries
grep -c '\[\[File:' FILE                                   # inline images present
grep -o '{{\(langx\|efn-ua\|harvnb\|cite SEP\|font color\)' FILE | sort -u  # leftover enwiki templates
grep -c 'llogari' FILE                                     # "accounts" mistranslation check
grep -c '^|-' FILE                                          # table row count, compare to source
```

Report the counts. They are the evidence that the citation apparatus survived. The image count should be non-zero for any article-length piece; zero means images were dropped. For a table-heavy article, also diff the `^|-` count and the `^==` section-heading count against the same greps run on the source file — a mismatch means a row or a whole section got dropped during assembly, which a spot-check of the rendered tables alone can miss.

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
