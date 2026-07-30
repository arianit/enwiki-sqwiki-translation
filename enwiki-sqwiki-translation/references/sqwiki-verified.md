# Verified sqwiki targets

Confirmed by Wikidata sitelinks (templates) or by a search result URL (articles). Wikis change — if something here fails, re-verify and correct this file.

## Templates confirmed present on sqwiki

| enwiki | sqwiki |
|---|---|
| `{{Infobox military conflict}}` | `Stampa:Infobox military conflict` |
| `{{Infobox medical condition}}` | `Stampa:Infobox medical condition` |
| `{{Infobox medical condition (old)}}` | `Stampa:Infobox sëmundje` |
| `{{Infobox philosopher}}` | `Stampa:Infobox filozof` — **name translated**; check whether its parameters are Albanian too |
| `{{Sfn}}` | `Stampa:Sfn` |
| `{{SfnRef}}` | `Stampa:SfnRef` |
| `{{Sfnmp}}` | `Stampa:Sfnmp` |
| `{{Tree list}}` | `Stampa:Tree list` |
| `{{Lang}}` | `Stampa:Lang` |
| `{{Lang-xx}}` variants | `Stampa:Lang-it`, `Stampa:Lang-sv`, `Stampa:Lang-jp` etc. — the family exists; check the specific code |
| `{{Link language}}` (marks an external link's language) | `Stampa:Ikonë gjuha` |

CS1 citation templates (`cite web`, `cite book`, `cite journal`, `cite news`, `citation`) and `{{Reflist}}` are in active use on sqwiki — Module:Citation/CS1 is installed.

## Templates NOT confirmed — check before using

- `{{flagicon image}}` — unverified. Flags in infoboxes are discouraged by MOS anyway; dropping the calls is a clean fallback.
- `{{KIA}}` — unverified; write `(i vrarë)` instead.
- `{{langx}}` — renamed on enwiki in 2024, likely absent on sqwiki. Use plain italics: `''anglisht'': ''Title''`.
- `{{small}}` — use `<small>…</small>`, which always works.
- `{{Harvnb}}` — unverified, though it very likely exists wherever `Stampa:Sfn` does. Until confirmed, expand bundled harvnb refs into consecutive `{{Sfn}}` calls.
- `{{efn-ua}}` / `{{notelist-ua}}`, `{{blockquote}}`, `{{Transliteration}}`, `{{font color}}`, `{{Flatlist}}`, `{{collapsible list}}`, `{{cite SEP}}`, `{{cite IEP}}` — all unverified. See the substitution table in SKILL.md.
- `{{lang}}` — **now confirmed as `Stampa:Lang`** (see above). `{{gjuha}}` is not the name; don't use it. Earlier drafts of this file wrongly listed both as unverified.
- `{{në gjuhën}}` for marking the language of an external link — unverified; plain parenthetical works.

## Article titles confirmed

| enwiki | sqwiki |
|---|---|
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

Two independent blocks, one of which the user can lift.

**`web_fetch` — URL provenance.** Only URLs that appeared in a prior search or fetch result can be fetched. An enwiki article URL surfaced by `web_search` works; editing it into `?action=raw`, `/w/api.php`, `/w/rest.php/v1/page/…` or `Special:Export` does not — the edited path is treated as unseen and rejected. So the wikitext source cannot be pulled this way even for articles you just fetched.

**`bash` — egress allowlist.** `curl` to `en.wikipedia.org` returns `403` with `x-deny-reason: host_not_allowed`. Wikipedia is not in the container's default allowed-domains list.

**The workaround worth proposing.** The allowlist is user-configurable. If the user adds `en.wikipedia.org` and `sq.wikipedia.org` to network egress settings, both bottlenecks disappear:

- `curl "https://en.wikipedia.org/w/index.php?title=X&action=raw"` gets the wikitext directly, removing the paste step and the degraded-rendered-page fallback entirely.
- `curl "https://sq.wikipedia.org/w/api.php?action=query&format=json&titles=A|B|C|…"` checks up to 50 link targets in a single call. This is the single biggest quality lever available: the reason long articles end up under-linked is that verification otherwise costs one `web_search` per target.

Mention this once when the article is long enough for it to matter. Don't nag.

**Until then:**
- `sq.wikipedia.org` is cache-only for `web_fetch` — pages cannot be fetched at all, only read through search snippets.
- `wikiwand.com/sq/...` mirrors sqwiki and appears in search results; presence there implies the sq article exists.
- Wikidata item pages are fetchable and searchable, and their sitelink lists resolve both article and template existence — the cheapest verification available without the allowlist change.
- Otherwise verification is one `web_search` per target. Budget accordingly and prioritise: titles in the lead and infobox first.
