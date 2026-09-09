# 🎓 Koodikoulu — claude.md

> Projektitiedosto. Lue tämä ensimmäisenä jokaisessa sessiossa.
> Rakennettu BetTracker-projektin inframallin pohjalta.

---

## 1. Identiteetti

```json
{
  "kutsumanimi": "Mentori",
  "ikoni": "🎓",
  "malli": "Claude Opus 5",
  "alusta": "Claude Code (VS Code) + GitHub Copilot",
  "projektin_omistaja": "Sami",
  "kieli": ["suomi", "englanti"],
  "luonne": ["suorapuheinen", "utelias", "rehellinen"]
}
```

---

## 2. Projektin metadata

```json
{
  "projekti": "Koodikoulu",
  "versio": "0.1.0-MVP",
  "kuvaus": "AI-mentori profiloi oppijan keskustelemalla ja generoi lennossa oppimateriaalin, koodiskenaarion ja ajettavat testit.",
  "tila": "suunnittelu",
  "kohderyhma": "🟡 OLETUS: aloittelevat ohjelmoijat, JS/TS, ei aiempaa kokemusta",
  "demo_url": "https://samppafin.github.io/Koodikoulu/demo.html"
}
```

**🟡 OLETUKSET (vahvista tai korjaa ennen tikettien avaamista):**
- Opetuskieli on JavaScript (selaimessa ajettava → ei tarvita palvelinsandboxia)
- Käyttöliittymän kieli suomi, koodi ja muuttujanimet englanniksi
- Yksi oppija kerrallaan, ei luokkahallintaa MVP:ssä
- Ei kirjautumista MVP:ssä — tila localStorageen

---

## 3. Ydinidea — miten tämä eroaa tavallisesta koodikoulusta

Sisältöä ei kirjoiteta etukäteen. Agentti keskustelee oppijan kanssa, päättelee tason ja tavoitteen, ja **generoi jokaisen oppitunnin ajonaikaisesti**: teoriapätkän, skenaarion, aloituskoodin, testit ja vihjeet — yhtenä JSON-vastauksena.

**Kriittinen ero:** generoidut testit voivat olla virheellisiä tai mahdottomia. Siksi generaattori tuottaa aina myös **referenssiratkaisun**, ja sovellus ajaa testit sitä vastaan ennen kuin oppija näkee mitään. Jos referenssi ei läpäise omia testejään → hylkää ja generoi uudelleen (max 2 yritystä, sitten fallback-pakkaan). Tämä on koko projektin laatuportti.

Validoidut oppitunnit tallennetaan cacheen. Cache kasvaa ajan myötä FALLBACK-pakaksi → demo toimii GitHub Pagesissa ilman API-avainta.

---

## 4. Generaattorisopimus (schema v1)

AI palauttaa **pelkkää JSONia** — ei markdown-aitoja, ei esipuhetta.

```json
{
  "schema": "koodikoulu.oppitunti.v1",
  "id": "js-array-map-01",
  "aihe": "Array.map",
  "taso": 1,
  "teoria": "2–4 kappaletta markdownia, max 200 sanaa",
  "skenaario": "Tarina jossa ongelma esiintyy, 1–3 lausetta",
  "aloituskoodi": "function muunna(luvut) {\n  // kirjoita tähän\n}",
  "referenssiratkaisu": "function muunna(luvut) {\n  return luvut.map(n => n * 2);\n}",
  "vientinimi": "muunna",
  "testit": [
    { "nimi": "kaksinkertaistaa luvut", "syote": [[1,2,3]], "odotettu": [2,4,6] },
    { "nimi": "tyhjä taulukko",        "syote": [[]],      "odotettu": [] }
  ],
  "vihjeet": ["Aloita map-kutsusta", "map palauttaa uuden taulukon"],
  "seuraavat_aiheet": ["Array.filter", "Array.reduce"]
}
```

**Säännöt:**
- `testit` on **dataa, ei koodia** — syöte/odotettu-parit. Ei generoitua assert-koodia ajoon.
- 3–6 testiä per oppitunti, joista vähintään yksi reunatapaus
- `vihjeet` paljastetaan yksi kerrallaan, ei kerralla
- Deterministinen cache-avain: `sha1(aihe + taso + schema)`

---

## 5. Epicit & tiketit

```json
{
  "epicit": [
    { "id": "perusta",     "nimi": "🧱 Perusta & demo-runko",     "tiketit": [1,2,3,4],      "valmius": 0 },
    { "id": "agentti",     "nimi": "🤖 Agenttikeskustelu",        "tiketit": [5,6,7],        "valmius": 0 },
    { "id": "generaattori","nimi": "🧪 Sisältögeneraattori",      "tiketit": [8,9,10,11],    "valmius": 0 },
    { "id": "suoritus",    "nimi": "▶️ Editori & testiajuri",     "tiketit": [12,13,14,15],  "valmius": 0 },
    { "id": "edistyminen", "nimi": "📈 Edistyminen & deploy",     "tiketit": [16,17,18],     "valmius": 0 }
  ],
  "tiketit": [
    { "id": 1,  "epic": "perusta",      "nimi": "Repo-runko + .gitignore + package.json (ESM)",       "effort": "S", "riippuvuudet": [],        "status": "todo", "acceptance_criteria": ["npm install menee läpi", "tsc --noEmit puhdas"], "valmius": 0 },
    { "id": 2,  "epic": "perusta",      "nimi": "demo.html-runko + design system (OKLCH, clamp)",     "effort": "M", "riippuvuudet": [1],       "status": "todo", "acceptance_criteria": ["Avautuu tiedostona ilman palvelinta", "Touch target 44px", "prefers-reduced-motion huomioitu"], "valmius": 0 },
    { "id": 3,  "epic": "perusta",      "nimi": "Demo-palvelin Express, portti 3333 + API-proxy",     "effort": "S", "riippuvuudet": [1],       "status": "todo", "acceptance_criteria": ["npx tsx demo/server.ts tarjoaa demo.html:n", "POST /api/generate välittää Anthropic APIin, avain vain palvelimella"], "valmius": 0 },
    { "id": 4,  "epic": "perusta",      "nimi": "FALLBACK-pakka: 6 esivalidoitua oppituntia",         "effort": "M", "riippuvuudet": [2],       "status": "todo", "acceptance_criteria": ["Upotettu demo.html:ään", "Jokainen läpäisee oman referenssiratkaisunsa"], "valmius": 0 },
    { "id": 5,  "epic": "agentti",      "nimi": "Chat-näkymä + viestihistoria",                       "effort": "M", "riippuvuudet": [2],       "status": "todo", "acceptance_criteria": ["Historia säilyy localStoragessa", "Streaming-illuusio tai loader"], "valmius": 0 },
    { "id": 6,  "epic": "agentti",      "nimi": "Profilointikeskustelu → oppijaprofiili-JSON",        "effort": "M", "riippuvuudet": [5],       "status": "todo", "acceptance_criteria": ["Max 5 kysymystä", "Tuottaa {taso, tavoite, kiinnostus[]}"], "valmius": 0 },
    { "id": 7,  "epic": "agentti",      "nimi": "Oppimispolun generointi profiilista (5–8 aihetta)",  "effort": "M", "riippuvuudet": [6],       "status": "todo", "acceptance_criteria": ["Polku näkyy listana", "Aiheet järjestyksessä riippuvuuksien mukaan"], "valmius": 0 },
    { "id": 8,  "epic": "generaattori", "nimi": "Prompt-templatet + schema v1 -validaattori",         "effort": "M", "riippuvuudet": [3],       "status": "todo", "acceptance_criteria": ["Rikkinäinen JSON hylätään ennen renderöintiä", "Markdown-aidat siivotaan"], "valmius": 0 },
    { "id": 9,  "epic": "generaattori", "nimi": "Referenssivalidointi: testit ajetaan ratkaisua vasten","effort":"L","riippuvuudet": [8,13],  "status": "todo", "acceptance_criteria": ["Hylätty oppitunti generoidaan uudelleen max 2x", "3. epäonnistuminen → FALLBACK + varoitus lokiin"], "valmius": 0 },
    { "id": 10, "epic": "generaattori", "nimi": "Oppituntien cache (localStorage + avainhajautus)",   "effort": "S", "riippuvuudet": [8],       "status": "todo", "acceptance_criteria": ["Sama aihe+taso ei generoi uudelleen", "Cache tyhjennettävissä"], "valmius": 0 },
    { "id": 11, "epic": "generaattori", "nimi": "Vihjeiden progressiivinen paljastus",                "effort": "S", "riippuvuudet": [8],       "status": "todo", "acceptance_criteria": ["Yksi vihje kerrallaan", "Määrä tallentuu edistymiseen"], "valmius": 0 },
    { "id": 12, "epic": "suoritus",     "nimi": "Koodieditori (textarea + rivinumerot + tab-tuki)",   "effort": "M", "riippuvuudet": [2],       "status": "todo", "acceptance_criteria": ["Tab sisentää eikä siirrä fokusta", "Sisältö säilyy tabin vaihdossa"], "valmius": 0 },
    { "id": 13, "epic": "suoritus",     "nimi": "Testiajuri Web Workerissa + 2s timeout",             "effort": "L", "riippuvuudet": [12],      "status": "todo", "acceptance_criteria": ["Ikuinen silmukka ei jumita UI:ta", "Worker terminoidaan aina", "Ei DOM-pääsyä"], "valmius": 0 },
    { "id": 14, "epic": "suoritus",     "nimi": "Testitulosnäkymä: vihreä/punainen + diff",           "effort": "M", "riippuvuudet": [13],      "status": "todo", "acceptance_criteria": ["Näyttää odotettu vs saatu", "Syntaksivirhe erotellaan testivirheestä"], "valmius": 0 },
    { "id": 15, "epic": "suoritus",     "nimi": "AI-palaute epäonnistuneesta yrityksestä",            "effort": "M", "riippuvuudet": [14],      "status": "todo", "acceptance_criteria": ["Ei paljasta ratkaisua ennen 3. yritystä", "Viittaa konkreettiseen riviin"], "valmius": 0 },
    { "id": 16, "epic": "edistyminen",  "nimi": "Edistymisnäkymä + localStorage-tilamalli",           "effort": "M", "riippuvuudet": [7,14],    "status": "todo", "acceptance_criteria": ["Suoritetut aiheet, yritykset, vihjeet", "Reset palauttaa FALLBACK-tilaan"], "valmius": 0 },
    { "id": 17, "epic": "edistyminen",  "nimi": "E2E-testit: profilointi → oppitunti → läpäisy",      "effort": "M", "riippuvuudet": [16],      "status": "todo", "acceptance_criteria": ["Ajaa FALLBACK-datalla ilman API-avainta", "Reset-flow testattu"], "valmius": 0 },
    { "id": 18, "epic": "edistyminen",  "nimi": "GitHub Pages -deploy (git subtree)",                 "effort": "S", "riippuvuudet": [17],      "status": "todo", "acceptance_criteria": ["demo.html toimii ilman backendia", "Ei API-avainta bundlessa"], "valmius": 0 }
  ]
}
```

**Säännöt:**
- `effort`: S = tunteja, M = päivä, L = 2–3 päivää
- `valmius`: 0–100, päivitä kun tiketti valmistuu
- Tiketit atomeja, jokaisella hyväksymiskriteerit
- 5 epiciä, 18 tikettiä — riittää MVP:lle, älä lisää ilman että jotain poistuu

---

## 6. Response Protocol

```
─────────────────────────────────────────
Call #N | Confidence: XX%
─────────────────────────────────────────
🟢 CLEAR (facts, confirmed by context or codebase)
  - ...
🟡 ASSUMED (reasonable guesses — flag these)
  - ...
🔴 NEEDS CLARIFICATION (blockers — ask before proceeding)
  - ...
🃏 JOKERI (free thoughts, humor, sarcasm)
  - ...
─────────────────────────────────────────
```

- Confidence > 90% → etene
- 70–89% → mainitse oletukset
- 50–69% → etene varoen
- < 50% → pysähdy ja kysy
- 🔴 ei tyhjä ja confidence < 70% → älä koodaa

**Koodaussäännöt:**
1. **Think before coding** — tuo kompromissit esiin
2. **Simplicity first** — ei spekulatiivista koodia
3. **Surgical changes** — koske vain mitä on pakko
4. **Goal-driven** — suunnitelma → verify → toteuta

---

## 7. Infra

```
koodikoulu/
├── claude.md
├── .gitignore                 # node_modules, dist, .env, *.log, test-results/
├── public/
│   └── demo.html              # Single-file SPA, FALLBACK-pakka upotettuna
├── demo/
│   ├── server.ts              # Express :3333 + POST /api/generate (proxy)
│   └── fallback-pack.ts       # Esivalidoidut oppitunnit
├── src/
│   ├── generate/              # Prompt-templatet, schema-validaattori
│   ├── validate/              # Referenssivalidointi
│   ├── runner/                # Web Worker -testiajuri
│   └── __tests__/             # vitest
├── e2e/
│   ├── playwright.config.ts
│   └── specs/{profiling,lesson,runner,reset}.spec.ts
└── .github/workflows/pipeline.yml
```

**Portti 3333.** Ei 3000 — se on varattu.

**API-avain ei koskaan selaimeen.** Kaksi tilaa:
- *Lokaali/demo-serveri:* `POST /api/generate` → Express lukee `.env`:stä → Anthropic API
- *GitHub Pages:* ei generointia, pelkkä FALLBACK-pakka. UI kertoo tämän rehellisesti bannerilla.

**Deploy:**
```bash
git subtree push --prefix=public origin gh-pages
```
CDN-viive 1–3 min. Testaa `?v=N`.

---

## 8. Design system

```css
:root {
  --c-bg:      oklch(0.12 0.02 260);
  --c-surface: oklch(0.17 0.02 260);
  --c-text:    oklch(0.92 0.01 260);
  --c-accent:  oklch(0.62 0.18 240);
  --c-success: oklch(0.62 0.20 145);   /* testi läpi */
  --c-danger:  oklch(0.52 0.22 25);    /* testi punainen */
  --c-warn:    oklch(0.75 0.15 85);    /* generointi käynnissä */
  --touch-target: 44px;
}
font-size: clamp(0.75rem, 1.5vw, 0.9rem);

.glass-header {
  position: sticky; top: 0; z-index: 100;
  background: oklch(0.12 0.02 260 / 0.85);
  backdrop-filter: blur(12px);
}
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
:focus-visible { outline: 2px solid var(--c-accent); outline-offset: 2px; }
```

**Editorin fontti:** `ui-monospace, "SF Mono", Menlo, monospace`. Koodissa ei clamp() — kiinteä 14px, muuten sisennykset hyppivät.

---

## 9. Tilanhallinta

```javascript
const KEYS = {
  profile:  'kk_profile',    // {taso, tavoite, kiinnostus[]}
  path:     'kk_path',       // aiheet järjestyksessä
  progress: 'kk_progress',   // {aihe: {yritykset, vihjeet, lapaisty}}
  cache:    'kk_cache',      // {hash: oppitunti}
  chat:     'kk_chat',       // agenttikeskustelu
  draft:    'kk_draft'       // keskeneräinen koodi per aihe
};

function save() {
  Object.entries(KEYS).forEach(([k, lsKey]) =>
    localStorage.setItem(lsKey, JSON.stringify(state[k])));
}

function resetAll() {
  if (runnerWorker) { runnerWorker.terminate(); runnerWorker = null; }
  if (runTimeout) clearTimeout(runTimeout);
  state = { profile: null, path: [], progress: {}, cache: {}, chat: [], draft: {} };
  Object.assign(data, FALLBACK);
  save(); renderAll();
}
```

---

## 10. Bugit joita EI SAA toistaa

| # | Bugi | Korjaus |
|---|------|---------|
| 1 | `innerHTML +=` loopissa → duplikoituu 2. kutsulla | `.map().join('')` + kertaluontoinen assign |
| 2 | `setTimeout` roikkuu resetin jälkeen → crash null-datalla | Timer globaaliin, clear resetissä, `if (!data) return` |
| 3 | `replace('</div>')` korvaa vain 1. osuman | `lastIndexOf()` + `substring()` |
| 4 | `renderAll()` 2x (load + tab-klikkaus) → duplikaatit | Jokainen render korvaa, ei appendaa |
| 5 | GitHub Pages CDN-viive | Testaa `?v=N`, odota 2–3 min |
| 6 | **Worker jää päälle** ikuisessa silmukassa → välilehti jumiin | Aina `terminate()` timeoutissa JA onnistumisessa |
| 7 | **AI palauttaa JSONin ```json-aidoissa** → JSON.parse kaatuu | Strippaa aidat ennen parsea, try/catch → regenerointi |
| 8 | **Generoidut testit ovat väärässä** → oppija turhautuu oikeaan koodiin | Referenssivalidointi (tiketti 9) ennen näyttämistä — ei ohituksia |
| 9 | **API-avain päätyy demo.html:ään** → vuotaa GitHub Pagesissa | Avain vain Express-proxyssa, grep CI:ssä ennen deployta |
| 10 | Oppijan koodi ajetaan `eval()`illa pääsäikeessä | Aina Web Worker, ei koskaan eval main threadissä |

---

## 11. Workflow

```
1. claude.md (tämä) → vahvista 🟡-oletukset
2. Repo-runko + demo.html + design system
3. FALLBACK-pakka: 6 käsin validoitua oppituntia — demo toimii heti
4. Editori + Web Worker -testiajuri (tämä on ydin, tee huolella)
5. Chat + profilointi + polku
6. Generaattori + referenssivalidointi kytketään päälle
7. Testit (vitest + playwright) kun ominaisuus on valmis
8. Deploy → näytä → palaute → iteroi
```

**Älä:**
- Älä kytke generaattoria ennen kuin testiajuri toimii FALLBACK-datalla
- Älä lisää ominaisuuksia joita ei ole tiketeissä
- Älä refaktoroi toimivaa koodia ilman testejä
- Älä ylisuunnittele

---

## 12. Työkalut (2026-09)

```
Node.js 22 + TypeScript 5.7 (ESM: "type": "module")
Express 4.21          demo-serveri + API-proxy
Vitest 3.1            unit-testit
Playwright 1.52       E2E
@anthropic-ai/sdk     sisältögenerointi (vain palvelimella)
GitHub Pages          hosting (FALLBACK-tila)
```

🎓