# JSON schemaer for Natur for Alle

Her finder du JSON schemaer for de JSON objekter, der kan indsættes i feltet `tilgaeng_opl`, der findes i de tre
friluftsliv temaer:

- t_5800_fac_pkt
- t_5801_fac_fl
- t_5802_fac_li

ALt efter hvilken type faciliteten har, skal der anvendes et specifikt schema:

## t_5800_fac_pkt

| facil_ty_k | facil_ty                | schema                                           |
|:-----------|:------------------------|:-------------------------------------------------|
| 1012       | Toilet                  | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1012.json |
| 1022       | Bålplads                | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1022.json |
| 1041       | Naturlegeplads          | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1041.json |
| 1051       | Badestrand/-sted        | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1051.json |
| 1132       | Tørvejrsrum/Madpakkehus | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1132.json |
| 1201       | Borde-bænke/Picnic      | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1201.json |
| 1291       | Holdeplads              | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1291.json |
| 1331       | Badebro                 | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1331.json |
| 1351       | Parkeringsplads         | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.1351.json |
| 2012       | Fugletårn/Udsigtstårn   | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.2012.json |
| 3012       | Shelter                 | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.3012.json |
| 3091       | Bålhytte                | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.3091.json |
| 4411       | Fiskeplatform           | t_5800_fac_pkt.tilgaeng_opl.facil_ty_k.441.json  |

## t_5802_fac_li

| rute_ty_k | rute_ty    |                   schema                    |
|:----------|------------|:-------------------------------------------:|
| 5         | Vandrerute | t_5802_fac_li.tilgaeng_opl.rute_ty_k.5.json |

## t_5801_fac_fl

**_Ingen schemaer_**

> **_Bemærk:_** De enkelte schemaer refererer til et centralt schema (tilgaeng_opl.json) hvori de forskellige
> definitioner findes.
> Denne metode er valgt, da flere schemaer refererer til samme definition.

## Validering af objekter

Inden man indsætter et objekt i GeoFA databasen kan du validere det. Der findes mange forskellige måder at gøre det på.
Det kan fx gøre online på [https://www.jsonschemavalidator.net/](https://www.jsonschemavalidator.net/). Prøv med dette
schema og objekt:

**Schema for Badebro (1331)**

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://geofa.geodanmark.dk/tilgaengelighed/schema/",
  "title": "Natur for alle",
  "description": "Badebro (1331)",
  "type": "object",
  "$ref": "tilgaeng_opl.json#/$defs/Badestrand~1sted og kategorien Badebro",
  "required": [
    "Er der adgang til vand uden trin og trapper (niveaufri)",
    "Hvordan er adgangen til vandet"
  ]
}
```

**Objekt**

```json
{
  "Er der adgang til vand uden trin og trapper (niveaufri)": true,
  "Hvordan er adgangen til vandet": [
    "Lift",
    "Trappe uden gelænder"
  ]
}
```

## Brug i QGIS

Når et friluftstema åbnes i QGIS gennem WFS vil JSON feltet blive behandlet som et alm. tekstfelt, hvor JSON objektet
kan indsættes eller ændres som tekst.

Hvis du ønsker at "flade" JSON objektet ud, således de enkelte JSON egenskaber optræder som kolonner i tabellen, kan du
anvende "virtuelle" felter.

Fx hvis du gerne vil have "Er der adgang til vand uden trin og trapper (niveaufri)" fra Badebro (1331) ud i sit eget
felt, skal du oprette et `boolean` virtuelt felt
og bruge følgende expresssion:

```text
map_get(from_json(tilgaeng_opl),'Er der adgang til vand uden trin og trapper (niveaufri)')
```

Hvis værdien selv er et JSON objekt kan du vælge et virtuelt `text` felt til at vise værdien som en JSON-tekst:

```text
to_json(map_get(from_json(tilgaeng_opl),'Hvordan er adgangen til vandet'))
```

Det er også muligt at "flade ud" flere led ned. Tag fx "Skriv adresse på parkeringspladsen, eller nærmeste adresse", som
anvendes i Holdeplads (1291) og Parkeringsplads (1351). Det ser sådan ud:

```json
{
  "Skriv adresse på parkeringspladsen, eller nærmeste adresse": {
    "vejnavn": "Søvej",
    "vejnummer": 35,
    "postnummer": 9000,
    "by": "Aalborg"
  }
}
```

Og du vil have et virtuelt felt med `by`. Så kan det gøres med denne expressions

```text
map_get(map_get(from_json(tilgaeng_opl),'Skriv adresse på parkeringspladsen, eller nærmeste adresse'),'by')
```


