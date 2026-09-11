# Koodikoulu — Oppimisloki

Tähän kerätään kaikki mitä opitaan Koodikoulua rakentaessa, jotta samasta rungosta
voi tehdä palvelun muillekin oppijoille ja muihin aiheisiin.

---

## Periaatteet (claude.md, vahvistettu)

- Sisältöä ei kirjoiteta etukäteen: agentti generoi oppitunnit ajonaikaisesti
  (teoria, skenaario, aloituskoodi, testit, vihjeet, referenssiratkaisu) yhtenä
  JSON-vastauksena, schema `koodikoulu.oppitunti.v1`.
- **Laatuportti:** generoidut testit ajetaan ensin referenssiratkaisua vasten.
  Ei läpi → regeneroi (max 2×) → fallback-pakka.
- Validoidut oppitunnit talletetaan cacheen; cache kasvaa fallback-pakaksi →
  demo toimii GitHub Pagesissa ilman API-avainta.
- Oppijan koodi ajetaan aina Web Workerissa, ei koskaan `eval` pääsäikeessä.
  2 s timeout, worker terminoidaan aina.
- API-avain vain palvelinproxyssa, ei koskaan selaimeen tai bundleen.

---

## Oppitunnin rakenne (vakioitu sessiossa 1)

Jokainen oppitunti / oppimissessio noudattaa samaa kaavaa:

1. **Intro / teoria** — käsitteet niin yksinkertaisesti että 10-vuotias ymmärtää.
   Numeroidut kortit + 2–3 inline-SVG-kaaviota.
2. **Lähtökysely** — 4 kysymystä: aiempi kokemus, kiinnostuksen kohteet,
   oppimistapa, tahti. → `kk_lahtokysely`
3. **Koodaustesti** — 4 monivalintaa, jotka haarukoivat tason 1–3. → `kk_testitulos`
4. **Loppuform** — mitä opit / mikä jäi epäselväksi / mitä seuraavaksi.
   Generoi markdown-tiketin `tickets/`-kansioon.
5. **Seuraava iteraatio** — tiketin pohjalta rakennetaan seuraava oppitunti.

---

## Tason arviointi (koodaustesti)

| Oikein | Taso | Sisältö |
|--------|------|---------|
| 0–1 | 1 | muuttujat, tyypit, ehdot, silmukat |
| 2–3 | 2 | funktiot, taulukot, oliot, array-metodit |
| 4   | 3 | korkeamman kertaluvun funktiot, async, virheenkäsittely |

---

## Design-päätökset

- claude.md:n OKLCH-designjärjestelmä, dark-first + light-toggle
  (`data-theme` juuressa, tila `kk_theme`).
- Lukusivun perusfontti hieman claude.md:n `clamp(0.75rem … 0.9rem)` -määrittelyä
  suurempi (`clamp(0.98rem … 1.1rem)`) luettavuuden vuoksi. Koodifontti kiinteä 14px.
- Ei kolmannen osapuolen kirjastoja. Yksi itsenäinen HTML-tiedosto per oppitunti
  (AI-Koulun malli: yksi sivu per aihe).
- Kaikki `localStorage`-luku/kirjoitus try/catchissä; sivu toimii myös tyhjällä tilalla.
- SVG-kaaviot teematietoisia: `fill`/`stroke` osoittavat CSS-muuttujiin.

---

## localStorage-avaimet

| Avain | Sisältö |
|-------|---------|
| `kk_theme` | "light" / "dark" |
| `kk_progress_l1` | oppitunti 1:n osioiden edistyminen |
| `kk_lahtokysely` | `{kokemus, kiinnostus[], tapa, tahti}` |
| `kk_testitulos` | `{oikein, yhteensa, taso}` |
| `kk_loppuform_l1` | `{nimi, opittu, epaselva, seuraava}` |
| `kk_draft` | `{aihe_id: koodiluonnos}` — claude.md §9:n kanoninen avain, käytössä oppitunti 2 alkaen |
| `kk_progress` | `{aihe_id: {yritykset, vihjeet, lapaisty}}` — kanoninen avain, käytössä oppitunti 2 alkaen |

> Huom: claude.md §9 määrittelee kanoniset avaimet (`kk_profile`, `kk_path`, …).
> Introsivu käyttää omia `_l1`-päätteisiä avaimia kunnes varsinainen tilamalli
> (tiketti 16) rakennetaan — silloin nämä mäpätään yhteen.

---

## Avoimet kysymykset

- Yhdistetäänkö oppituntisivut lopulta yhdeksi SPA:ksi (`public/demo.html`) vai
  pidetäänkö erillisinä sivuina per aihe?
- Miten `tickets/`-kansion tiketit syötetään takaisin generaattorille (käsin vs. skripti)?
- claude.md:n tiketit 1–18 vs. tämä introsivu — päivitetäänkö tiketit vastaamaan?
- claude.md §2:n 🟡-oletukset (JS, suomi UI, yksi oppija, ei kirjautumista) —
  vielä virallisesti vahvistamatta.

---

## Sessiot

### 2026-09-09 — Sessio 1

- Luettu `claude.md`.
- Sami linjasi: introsivu on **malli jokaiselle oppitunnille** —
  teoria (10v-taso) → koe → kysely → uusi iteraatio seuraavasta oppitunnista.
- Rakennettu `public/oppitunti-01.html`:
  - Infografiikka: 11 numeroitua korttia + 3 inline-SVG-kaaviota
    (ajoketju, ohjelmistotuotannon kehä, abstraktiokerrokset).
  - Lähtökysely, koodaustesti (tasoarvio 1–3), loppuform + tiketti­generaattori.
- Luotu `tickets/`-kansio, `tickets/README.md`, `tickets/001-oppitunti-01-intro.md`.
- **Seuraava:** Ohto käy sivun läpi → loppuform tuottaa `tickets/002-*.md` →
  rakennetaan oppitunti 2 sen pohjalta.
- Repo pushattu: https://github.com/SamppaFIN/Koodikoulu (main-haara).

### 2026-09-11 — Sessio 2

- Ohto suoritti oppitunnin 1: koe 2/4 oikein → taso 2, kiinnostus pelit,
  tahti rauhallinen, tapa "teoria ensin". Tallennettu `tickets/002-*.md`.
- Rakennettu `public/oppitunti-02.html` (Taulukot ja Array.map, taso 2):
  - Teoria + 1 SVG-kaavio (map muuntaa taulukon, alkuperäinen säilyy).
  - Skenaario sidottu peleihin (pelaajien pisteiden tuplaus bonuskierroksella).
  - Oikea koodieditori: rivinumerot, Tab sisentää, luonnos talteen `kk_draft`.
  - **Testiajuri Web Workerissa** (ensimmäinen kerta): 5 testiä (data, ei koodia),
    2 s timeout, worker terminoidaan aina onnistuessa/timeoutissa/virheessä,
    syntaksivirhe erotellaan testivirheestä. `new Function` vain workerin
    sisällä — ei koskaan pääsäikeessä (bugi #10).
  - Referenssiratkaisu (`tuplaaPisteet`) validoitu Node.js:llä ennen julkaisua —
    kaikki 5 testiä vihreänä. Ratkaisua ei upoteta sivulle, ettei se vuoda oppijalle.
  - Vihjeet yksi kerrallaan, määrä + yritykset + läpäisy tallennettu `kk_progress`-avaimeen.
  - Yhteenveto-lomake generoi `tickets/003-*.md`:n, taso ehdotetaan tuloksesta
    (0 vihjettä + kaikki läpi → nosta tasoa 3; ei yhtään läpi → laske tasoa 1).
- **Avainkäytäntö muuttui:** oppitunti 2 alkaen käytetään claude.md §9:n kanonisia
  avaimia `kk_draft` / `kk_progress` (aiemmin oppitunti 1:n omat `_l1`-avaimet —
  ne jäävät ennalleen, eivät riko mitään, mutta eivät ole enää malli uusille sivuille).
- **Seuraava:** Ohto suorittaa oppitunnin 2 → `tickets/003-*.md` → oppitunti 3.
