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

## Templates confirmed present on sqwiki (medical/CS1 batch, verified 2026-07-31)

`Stampa:CO2`, `Stampa:Commons category`, `Stampa:Div col` / `Stampa:Div col end`, `Stampa:Further`, `Stampa:Infobox medical intervention` (English-passthrough params: `Name`, `Image`, `Caption`, `ICD10`, `ICD9`, `MeshID`, `OPS301`, etc. — only the infobox *labels* are localized), `Stampa:Main`, `Stampa:Ndash`, `Stampa:Nowrap`, `Stampa:Refbegin` / `Stampa:Refend`, `Stampa:Reflist`, `Stampa:Rp`, `Stampa:See also`, `Stampa:Short description`, `Stampa:TOC limit`.

**Missing (verified 2026-07-31):** `Stampa:Portal` (single-portal form; drop), `Stampa:Retracted` (substitute a plain parenthetical note inside the `<ref>` with a link to the retraction DOI), `Stampa:Nbhyph` (use a plain hyphen), `Stampa:ICD9proc` (write the code as plain text), `Stampa:En icon` / `Stampa:Anglisht` (use a plain `(anglisht)` parenthetical).

## Templates confirmed present on sqwiki (classical-biography batch, verified 2026-08-01)

`Stampa:Blockquote`, `Stampa:Circa`, `Stampa:Collapsible list`, `Stampa:Coord`, `Stampa:E` (scientific notation), `Stampa:Efn`, `Stampa:Harvnb` (now confirmed — no need to expand bundled harvnb refs into `{{Sfn}}` calls), `Stampa:Internet Archive author`, `Stampa:IPA`, `Stampa:IPAc-en`, `Stampa:ISBN`, `Stampa:Math`, `Stampa:Nbsp`, `Stampa:Other uses`, `Stampa:Pb` (paragraph break inside one ref), `Stampa:Respell`, `Stampa:Sfrac`, `Stampa:Sister project links`, `Stampa:Webarchive`, `Stampa:Wikt-lang`.

**Missing (verified 2026-08-01):** `Stampa:EB1911 poster`, `Stampa:Expand section` (maintenance tag; drop), `Stampa:Gpn` (Gazetteer of Planetary Nomenclature; substitute a plain external link), `Stampa:Gutenberg author` (substitute `https://www.gutenberg.org/ebooks/author/ID`), `Stampa:In Our Time`, `Stampa:InPho`, `Stampa:JSTOR` (only the bare standalone template is missing — the `|jstor=` CS1 *parameter* inside `cite` templates works fine via Module:Citation/CS1; only substitute when `{{JSTOR|ID}}` is used bare outside a citation template), `Stampa:MathPages` (substitute `https://www.mathpages.com/PATH/PATH.htm`), `Stampa:PhilPapers` (substitute `https://philpapers.org/s/QUERY`), `Stampa:Pi` (just write the literal `π` character — simpler than chasing a template name).

## Templates NOT confirmed — check before using

- `{{flagicon image}}` — unverified. Flags in infoboxes are discouraged by MOS anyway; dropping the calls is a clean fallback.
- `{{KIA}}` — unverified; write `(i vrarë)` instead.
- `{{langx}}` — renamed on enwiki in 2024, likely absent on sqwiki. Use plain italics: `''anglisht'': ''Title''`.
- `{{small}}` — use `<small>…</small>`, which always works.
- `{{efn-ua}}` / `{{notelist-ua}}`, `{{Transliteration}}`, `{{font color}}`, `{{Flatlist}}`, `{{cite SEP}}`, `{{cite IEP}}` — all unverified. See the substitution table in SKILL.md.
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
| Vaccination | *no sq article* (confirmed via Wikidata sitelinks + direct title lookup 2026-07-31); related but distinct: `Imunizimi` (translates enwiki's "Immunization", short/654 words) and `Vaksinimi i fëmijëve` both exist |
| smallpox | `Lia` |
| poliomyelitis / polio | `Poliomieliti` |
| tetanus | `Tetanosi` (not `Tetanozi`) |
| measles | `Fruthi` |
| diphtheria | `Difteria` |
| tuberculosis | `Tuberkulozi` |
| rabies | `Tërbimi` |
| cholera | `Kolera` |
| typhoid | `Tifoja` |
| hepatitis B | `Hepatiti B` |
| meningitis | `Meningjiti` |
| cancer | `Kanceri` |
| Alzheimer's disease | `Sëmundja e Alzheimerit` |
| autism | `Autizmi` |
| COVID-19 / COVID-19 pandemic | `COVID-19` / `Pandemia e COVID-19` |
| World Health Organization | `Organizata Botërore e Shëndetësisë` |
| UNICEF | `UNICEF` |
| Edward Jenner | `Edward Jenner` (unchanged) |
| Louis Pasteur | `Lui Pastër` — **enwiki spelling `Louis Pasteur` does NOT resolve**; sqwiki uses the transliterated form as the actual title |
| influenza | `Gripi` |
| immune system | `Sistemi i imunitetit` |
| allergy | `Alergjia` |
| aluminium (element) | `Alumini` |
| mercury (element) | `Mërkuri (element)` redirects to `Zhiva` (the Albanian/Balkan name is primary; "Mërkuri (element)" works as a redirect) |
| narcolepsy | `Narkolepsia` (not `Narkolepsi`) |
| organism | `Organizmi` (not `Organizëm`) |
| bacteria | `Bakteri` (singular; `Bakterie`/`Bakteret` do not resolve directly) |
| Serbia | `Serbia` (not `Serbi`) |
| Kragujevac | `Kragujevci` (not `Kragujevac`) |
| Mumbai/Bombay | `Mumbai`; `Bombei` redirects there and works as a link target |
| Prussia | `Prusia` |
| public health | `Shëndeti publik` |
| protein | `Proteina` |
| game theory | `Teoria e lojës` (singular "game", not `Teoria e lojërave`) |
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
| Archimedes | `Arkimedi` (exists; rewrite target — infobox/lead were sound but the body was informal, apocryphal, and had zero inline citations) |
| Isaac Newton | `Isak Njutoni` — **enwiki spelling does not resolve**; Albanianized form is the real title, same pattern as Louis Pasteur → Lui Pastër |
| René Descartes | `Rëne Dekart` |
| Leonardo da Vinci | `Leonardo da Vinçi` (note the ç) |
| Cicero | `Ciceroni` |
| Strabo | `Straboni` |
| Plutarch | `Plutarku` |
| Polybius | `Polibi` |
| Livy | `Tit Livi` (not just `Livi`) |
| Lucian of Samosata | `Lukiani` |
| Apollonius of Perga | `Apolloni i Pergës` |
| Aristarchus of Samos | `Aristarku i Samosit` |
| Democritus | `Demokriti` |
| Eratosthenes | `Eratosteni` |
| Euclid | `Euklidi` |
| Galen | `Galeni` |
| Ptolemy | `Ptolemeu` |
| Carl Friedrich Gauss | `Carl Friedrich Gauss` (unchanged) |
| Christiaan Huygens | `Christiaan Huygens` (unchanged) |
| Antikythera mechanism | `Mekanizmi i Antikiterës` |
| classical antiquity | `Lashtësia klasike` (not `Antikiteti klasik`) |
| Fields Medal | `Medalja Fields` (not `Çmimi Fields`) |
| Pi (mathematical constant) | `Numri pi` |
| Eudoxus of Cnidus, Eutocius of Ascalon, Gottfried Wilhelm Leibniz, Hiero II of Syracuse, Isidore of Miletus, Conon of Samos, Pappus of Alexandria, Vitruvius, Syracuse (city), Siege of Syracuse, Archimedes' screw, Claw of Archimedes, Archimedes Palimpsest, mathematical physics, hydrostatics | all *no sq article found* despite multiple spelling attempts — left as plain text |

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
curl -s "https://sq.wikipedia.org/w/api.php?action=query&format=json&redirects=1&titles=A|B|C"

# Verify existence via Wikidata sitelinks — batch multiple enwiki titles per call, don't loop one-at-a-time
curl -s "https://www.wikidata.org/w/api.php?action=wbgetentities&sites=enwiki&titles=A|B|C&props=sitelinks&format=json"

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

**Wikidata sitelink checks get silently rate-limited when done one-title-per-request.** Issuing one `wbgetentities?sites=enwiki&titles=X` call per title in a loop hits Wikidata/sqwiki rate limits after roughly 15–20 rapid requests; the response body then comes back empty or non-JSON, and a naive parser reports these as `MISSING` — a false negative that leaves real, existing sqwiki articles wrongly unlinked. Always batch multiple `enwiki` titles into one request with `|`-joined `titles=` (same mechanism as the Action API batching above), and add a short pause between batches for very long lists. Never trust a single "MISSING" result from an unbatched, rapid-fire loop — re-verify with a proper batched call before concluding a term has no sqwiki article.

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
