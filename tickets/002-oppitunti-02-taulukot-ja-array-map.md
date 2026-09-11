# Tiketti 002 — Oppitunti 2: Taulukot ja Array.map

- luotu: 2026-09-11
- epic: oppitunnit
- status: doing
- oppija: Ohto
- pohjautuu: oppitunti-01 (intro)

## Lähtötaso

- Aiempi kokemus: En koskaan
- Kiinnostuksen kohteet: Pelit, En tiedä
- Oppimistapa: Teoria ensin
- Tahti: Rauhallinen
- Koodaustesti: 2/4 oikein → arvioitu taso 2

## Mitä oppija kertoi oppineensa

jotain koodia vaan ja vähän näitä mekaniikkoja

## Mikä jäi epäselväksi

sama kuin kysymys mitä jäi mieleen tästä oppitunnista

## Seuraava oppitunti

- Aihe: Taulukot ja Array.map
- Taso: 2
- Peruste: oppijan oma valinta + koodaustestin tasoarvio

## Acceptance criteria

- [x] Oppitunti generoitu schema `koodikoulu.oppitunti.v1` mukaan (aihe: Taulukot ja Array.map, taso: 2)
- [x] Referenssiratkaisu läpäisee omat testinsä ENNEN kuin oppija näkee mitään (`tuplaaPisteet`, tarkistettu Node.js:llä ennen julkaisua, ei mukana sivulla)
- [x] 5 testiä (3–6 vaatimus täyttyy), joista yksi reunatapaus (tyhjä taulukko)
- [x] Vihjeet paljastetaan yksi kerrallaan (3 kpl, painike laskee `kk_progress`-avaimeen)
- [ ] Oppija saa vähintään yhden testin vihreäksi

## Toteutus

`public/oppitunti-02.html` — teoria (taulukot + map, pelianalogia) → skenaario (bonuskierros
tuplaa pisteet) → editori (rivinumerot, Tab-tuki, Web Worker -testiajuri 2 s timeoutilla,
worker terminoidaan aina) → vihjeet → yhteenveto-lomake joka generoi tiketin 003.
Siirretty käyttämään claude.md §9:n kanonisia avaimia `kk_draft` / `kk_progress`.

## Muistiinpanot generaattorille

- Kieli yhtä yksinkertaista kuin introssa — 10-vuotias ymmärtää.
- Käyttöliittymä suomeksi, muuttuja- ja funktionimet englanniksi.
- Skenaario liitettävä oppijan kiinnostuksen kohteisiin (pelit).

## Seuraava

Ohto suorittaa oppitunnin 2 → yhteenveto-lomake tuottaa `tickets/003-*.md` →
oppitunti 3 rakennetaan sen pohjalta.
