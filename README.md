# samatry

Savitaipaleen Matkailu SAMAT ry:n verkkosivusto. Julkaistaan GitHub Pagesina tästä
repositoriosta.

Yhdistys toimi aiemmin nimellä Savitaipaleen aurinkosähkö ry osoitteessa sasry.fi.
Vanha sivusto on arkistoitu tähän repositorioon kokonaisuudessaan, ja siihen linkataan
uuden sivuston arkisto-osiosta.

## Rakenne

| Polku | Sisältö |
| --- | --- |
| `archive/index.html` | Arkiston aloitussivu (suomeksi, selittää mistä on kyse) |
| `archive/sasry.fi/` | Muuttumaton peilikopio vanhasta sivustosta |
| `.nojekyll` | Estää Jekyll-käsittelyn GitHub Pagesissa |

Uuden sivuston sivut tulevat repositorion juureen; rakenne määritellään yhdistyksen
uusien sääntöjen pohjalta.

## Paikallinen esikatselu

```sh
python3 -m http.server 8000
# http://localhost:8000/archive/
```
