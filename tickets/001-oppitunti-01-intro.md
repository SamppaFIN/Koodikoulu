# Tiketti 001 — Oppitunti 1: Intro (mitä tietokoneet ja ohjelmistotuotanto ovat)

- luotu: 2026-09-09
- epic: oppitunnit
- status: done
- oppija: Ohto
- pohjautuu: —

## Tavoite

Antaa aloittelijalle iso kuva ennen ensimmäistä koodiriviä: mikä tietokone on,
mitä ohjelma on, mitä "ohjelmistotuotanto" tarkoittaa — kaikki niin
yksinkertaisesti että 10-vuotias ymmärtää. Toimii mallina jokaiselle
seuraavalle oppitunnille.

## Sisältö

`public/oppitunti-01.html` — itsenäinen HTML-sivu, claude.md:n OKLCH-designjärjestelmä,
ei kirjastoriippuvuuksia, avautuu tiedostona ilman palvelinta.

1. **Infografiikka** — 11 numeroitua korttia:
   kone tottelee kirjaimellisesti · kaikki on numeroita · prosessori/muisti/levy ·
   ohjelma = resepti · koodikieli tulkkina · ajoketju · ohjelmistotuotanto on kehä ·
   bugit & testit · git = tallennuspisteet · pala kerrallaan · sanasto.
   Lisäksi 3 inline-SVG-kaaviota: ajoketju, ohjelmistotuotannon kehä, abstraktiokerrokset.
2. **Lähtökysely** — kokemus, kiinnostuksen kohteet, oppimistapa, tahti → `kk_lahtokysely`
3. **Koodaustesti** — 4 monivalintaa → `kk_testitulos` `{oikein, yhteensa, taso}`
   - 0–1 oikein → taso 1 · 2–3 → taso 2 · 4 → taso 3
4. **Loppuform** — mitä opit / mikä jäi epäselväksi / mitä seuraavaksi →
   generoi ladattavan & kopioitavan markdown-tiketin (`tickets/002-*.md`)

## Acceptance criteria

- [x] Avautuu tiedostona ilman palvelinta
- [x] Touch target 44px, `prefers-reduced-motion` huomioitu, `:focus-visible` näkyvä
- [x] Light/dark-toggle, tila localStoragessa
- [x] Koodaustesti antaa tasoarvion 1–3
- [x] Loppuform generoi ladattavan/kopioitavan markdown-tiketin
- [ ] Ohto on käynyt sivun läpi ja `tickets/002-*.md` on luotu

## Seuraava

Ohton loppuform → `tickets/002-<aihe>.md` → oppitunti 2 rakennetaan sen pohjalta.
Kieli pidetään yhtä yksinkertaisena kuin introssa; skenaario sidotaan oppijan
kiinnostuksen kohteisiin.
