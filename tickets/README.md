# tickets/

Oppituntien iteraatiotiketit. Jokainen oppitunti tuottaa loppuformissaan
seuraavan tiketin tähän kansioon, ja seuraava oppitunti rakennetaan sen pohjalta.

> Nämä ovat eri asia kuin `claude.md` §5:n rakennustiketit 1–18.
> Ne koskevat sovelluksen infraa; nämä koskevat yksittäisiä oppitunteja.

## Nimeäminen

```
NNN-oppitunti-NN-<slug>.md
```

- `NNN` — juokseva numero, kolme numeroa (`001`, `002`, …)
- `<slug>` — aihe pienin kirjaimin, väliviivoin

## Elinkaari

1. Oppitunnin loppuform generoi markdownin → tallenna tähän kansioon.
2. Tiketti luetaan → generaattori rakentaa seuraavan oppitunnin sen pohjalta.
3. `status`: `todo` → `doing` → `done`

## Kentät

- `luotu`, `epic`, `status`, `oppija`, `pohjautuu`
- **Lähtötaso** — lähtökyselyn vastaukset + koodaustestin tulos ja tasoarvio
- **Mitä oppija kertoi oppineensa**
- **Mikä jäi epäselväksi**
- **Seuraava oppitunti** — aihe + taso + peruste
- **Acceptance criteria** — mm. referenssivalidointi ennen näyttämistä
- **Muistiinpanot generaattorille**
