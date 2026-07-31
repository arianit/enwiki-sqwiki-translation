# Verified sqwiki targets

Confirmed by Wikidata sitelinks (templates) or by a search result URL (articles). Wikis change — if something here fails, re-verify and correct this file.

## Templates confirmed present on sqwiki

| enwiki | sqwiki |
|---|---|
| `{{Infobox military conflict}}` | `Stampa:Infobox military conflict` |
| `{{Infobox medical condition}}` | `Stampa:Infobox medical condition` |
| `{{Infobox medical condition (old)}}` | `Stampa:Infobox sëmundje` |
| `{{Infobox philosopher}}` | `Stampa:Infobox filozof` — **name translated; parameters are a mix** (see confirmed parameter list below) |
| `{{Sfn}}` | `Stampa:Sfn` |
| `{{SfnRef}}` | `Stampa:SfnRef` |
| `{{Sfnmp}}` | `Stampa:Sfnmp` |
| `{{Tree list}}` | `Stampa:Tree list` |
| `{{Lang}}` | `Stampa:Lang` |
| `{{Lang-xx}}` variants | `Stampa:Lang-it`, `Stampa:Lang-sv`, `Stampa:Lang-jp` etc. — the family exists; check the specific code |
| `{{Link language}}` (marks an external link's language) | `Stampa:Ikonë gjuha` |

CS1 citation templates (`cite web`, `cite book`, `cite journal`, `cite news`, `citation`) and `{{Reflist}}` are in active use on sqwiki — Module:Citation/CS1 is installed.

### Stampa:Infobox filozof — confirmed parameter names (verified 2026-07-30 via template talk page)

Albanian parameters (use these, not the English equivalents):

| parameter | meaning |
|---|---|
| `emri` | name |
| `lindja` | birth date |
| `vendlindja` | birthplace |
| `vdekja` | death date |
| `vendvdekja` | place of death |
| `shkolla_tradita` | school / tradition |
| `interesimet` | main interests |
| `ndikimet` | influences |
| `ndikoi_te` | influenced |
| `idetë` | notable ideas |

English passthrough parameters (keep as-is from enwiki):

`region`, `era`, `image`, `image_caption`, `signature`, `color`

Common wrong guesses that silently lose content: `data_lindjes`, `data_vdekjes`, `imazhi`, `nënshkrim`, `rajoni`, `epoka`, `shkolla`, `interesat_kryesore`, `idetë_e_njohura`, `ndikoi`.

## Templates NOT confirmed — check before using

- `{{flagicon image}}` — unverified. Flags in infoboxes are discouraged by MOS anyway; dropping the calls is a clean fallback.
- `{{KIA}}` — unverified; write `(i vrarë)` instead.
- `{{langx}}` — renamed on enwiki in 2024, likely absent on sqwiki. Use plain italics: `''anglisht'': ''Title''`.
- `{{small}}` — use `<small>…</small>`, which always works.
- `{{Harvnb}}` — unverified, though it very likely exists wherever `Stampa:Sfn` does. Until confirmed, expand bundled harvnb refs into consecutive `{{Sfn}}` calls.
- `{{efn-ua}}` / `{{notelist-ua}}`, `{{blockquote}}`, `{{Transliteration}}`, `{{font color}}`, `{{Flatlist}}`, `{{collapsible list}}`, `{{cite SEP}}`, `{{cite IEP}}` — all unverified. See the substitution table in SKILL.md.
- `{{lang}}` — **now confirmed as `Stampa:Lang`** (see above). `{{gjuha}}` is not the name; don't use it. Earlier drafts of this file wrongly listed both as unverified.
- `{{në gjuhën}}` for marking the language of an external link — unverified; plain parenthetical works.
- `{{Gjuha}}` — **wrong name**; the correct template is `{{Ikonë gjuha}}` (see confirmed above). Earlier drafts wrongly used `{{Gjuha|en}}` inside `<ref>` tags.

## Known sqwiki CS1 module bugs

### `|language=grc` crashes (verified 2026-07-31)

sqwiki's `Module:Citation/CS1` has `grc` in `lang_tag_remap` but the corresponding `lang_name_remap` entry is nil or missing its second element. Every cite template carrying `|language=grc` throws:

> Lua error te Moduli:Citation/CS1 te rreshti 1763: attempt to index field '?' (a nil value)

**Fix:** remove `|language=grc` from all cite templates. Perseus and TLG links make the Greek context obvious. Do not substitute `|language=el` (Modern Greek — wrong).

This affects exactly the number of `|language=grc` cite templates in the article. Count them with `grep -c 'language=grc' FILE` and expect that many errors if you leave them in.

## Article titles confirmed

| enwiki | sqwiki |
|---|---|
| Socrates | `Sokrati` (exists; short stub — rewrite target. Confirmed via search result URL 2026-07-30) |
| Trial of Socrates | `Gjyqi i Sokratit` (confirmed via search result URL) |
| Socratic method | `Metoda sokratike` (confirmed via search result URL) |

| Pashalik of Scutari | `Pashallëku i Shkodrës` |
| Kara Mahmud Pasha | `Mahmud Bushati` (displays as *Kara Mahmud pashë Bushati*) |
| Pashalik of Berat | `Pashallëku i Beratit` |
| Bosnia Eyalet | `Pashallëku i Bosnjës` |
| Old Montenegro | `Mali i Zi i Vjetër` |
| Brda (Montenegro) | `Kodrat e Malit të Zi` |
| Tribes of Montenegro | `Fiset e Malit të Zi` |
| Petar I Petrović-Njegoš | `Petri I Petroviq-Njegosh` |
| Petar II Petrović-Njegoš | `Petri II Petroviq-Njegosh` |
| Cetinje | `Çetina` (search result URL; displays *Cetina* — pipe it) |
| Measles | `Fruthi` (exists, but old and unreferenced — a rewrite target) |
| Piperi (tribe) | `Piperët` |
| Aristotle | `Aristoteli` (exists; old) |
| Platonic dialogues | `Dialogjet e Platonit` (confirmed via page title 2026-07-30) |
| Aristotelian ethics | `Etika aristoteliane` |
| Peripatetic school | `Shkolla peripatetike` |
| Bjelopavlići | `Palabardhi (Mali i Zi)` |
| Brda (Montenegro) region article | `Kodrat e Malit të Zi` |
| Battle of Krusi | *no sq article*; sqwiki prose calls it `Beteja e Krusit` |
| Principality of Montenegro | `Principata e Malit të Zi` |
| History of Montenegro | `Historia e Malit të Zi` |

## Phrasings sqwiki uses in running prose

Useful as link targets, though the article titles behind them weren't separately confirmed:

- *Princi-peshkopata e Malit të Zi* — Prince-Bishopric of Montenegro
- *Kisha Ortodokse Serbe* — Serbian Orthodox Church

## Access constraints

Two independent blocks. The user on this machine has enabled the egress allowlist for both wikis, so curl works — but document the default state for portability.

**`web_fetch` — URL provenance.** Only URLs that appeared in a prior search or fetch result can be fetched. An enwiki article URL surfaced by `web_search` works; editing it into `?action=raw`, `/w/api.php`, `/w/rest.php/v1/page/…` or `Special:Export` does not — the edited path is treated as unseen and rejected. So the wikitext source cannot be pulled via `web_fetch` even for articles you just fetched.

**`bash` / `curl` — egress allowlist.** By default, `curl` to `en.wikipedia.org` and `sq.wikipedia.org` returns `403 x-deny-reason: host_not_allowed`. The allowlist is user-configurable.

**Current state for this user:** both `en.wikipedia.org` and `sq.wikipedia.org` are in the egress allowlist. Use `curl` directly:

```bash
# Fetch enwiki raw wikitext
curl -s "https://en.wikipedia.org/wiki/ARTICLE?action=raw" > ARTICLE_enwiki_raw.wiki

# Verify sqwiki article existence (up to 50 titles in one call)
curl -s "https://sq.wikipedia.org/w/api.php?action=query&format=json&titles=A|B|C"

# Fetch sqwiki template page to verify parameters
curl -s "https://sq.wikipedia.org/wiki/Stampa:TEMPLATE_NAME?action=raw"
curl -s "https://sq.wikipedia.org/wiki/Stampa_diskutim:TEMPLATE_NAME?action=raw"

# Fetch sqwiki CS1 module to debug Lua errors
curl -s "https://sq.wikipedia.org/w/index.php?title=Moduli:Citation/CS1&action=raw" | sed -n 'LINE_NUMp'
```

**If curl stops working** (user revoked access or new machine), fall back to:
- `web_search` for article existence — result URL confirms title and existence
- `wikiwand.com/sq/...` mirrors sqwiki and appears in search results; presence there implies the sq article exists
- Wikidata item pages are fetchable via `web_fetch` and their sitelink lists resolve both article and template existence
- Otherwise verification is one `web_search` per target — budget accordingly, prioritise lead and infobox titles first

**Propose enabling the allowlist** when starting a long article if curl is blocked. It's the single biggest quality lever: bulk link verification via the API eliminates the reason long articles end up under-linked.

## Albanian grammar quick-reference

### Loanword plurals — palatalization before `-je`/`-jet`

Words ending in `-g` usually palatalize (g → gj) in the plural. Do not apply native Albanian noun suffixes blindly.

| singular | indef. pl. | def. pl. nom. | def. pl. gen./dat. | note |
|---|---|---|---|---|
| dialog | dialogje | dialogjet | dialogjeve | confirmed: sqwiki article `Dialogjet e Platonit` |
| blog | blogje | blogjet | blogjeve | by analogy |

Errors seen: `dialogët`, `dialogëve`, `dialoge` — all wrong.

### Wikilinks must be inflected for grammatical case

The display text of a wikilink must agree with the case required by the surrounding sentence. The link target stays in its canonical (nominative) form; the pipe provides the inflected display.

| context | wrong | right |
|---|---|---|
| after `në` (accusative) | `Në [[Athina Klasike]]` | `Në [[Athina Klasike|Athinën Klasike]]` |
| after `në` (accusative) | `Në [[Franca e Re]]` | `Në [[Franca e Re\|Francën e Re]]` |
| genitive `e/i/të` | usually handled by pipe already | check after all prepositions |

Check every wikilink that appears after a preposition or in object position.

### Vocabulary: "accounts / sources"

`llogaritë` = financial accounts or login accounts — **never use for historical sources**.

| intended meaning | use |
|---|---|
| testimonies, evidence | `dëshmi` (sg. `dëshmi`, pl. `dëshmi` / `dëshmitë`) |
| written narratives | `rrëfime` (sg. `rrëfim`, pl. `rrëfime` / `rrëfimet`) |
| sources in general | `burime` (sg. `burim`, pl. `burime` / `burimet`) |
| explanation of something | `shpjegim` |

### Era abbreviations

In prose and infoboxes, use `p.e.s.` (para erës sonë) and `e.s.` (erës sonë). Do not hyperlink or spell out once the abbreviation is introduced. In category names, `para erës sonë` is spelled out and stays as-is.
