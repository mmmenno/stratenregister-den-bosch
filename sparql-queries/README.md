# SPARQL queries

Alle queries (tenzij anders aangegeven) kunnen op de [Wikidata Query Service](https://query.wikidata.org/) worden gedraaid.


### alle straten met coördinaten, link naar Bossche Encyclopedie en link naar BAG indien aanwezig

```
SELECT ?item ?itemLabel ?bagid ?baglink ?belink ?coords WHERE {
  VALUES ?openbruimte { wd:Q79007 wd:Q174782 wd:Q3558430 }
  ?item wdt:P31 ?openbruimte . 
  ?item wdt:P131 wd:Q9807 .
  OPTIONAL{
    ?item wdt:P5207 ?bagid .
    BIND(CONCAT('https://bag.basisregistraties.overheid.nl/bag/id/openbare-ruimte/',?bagid) AS ?baglink)
  }
  OPTIONAL{
    ?item wdt:P973 ?belink .
    FILTER(REGEX(STR(?belink),"bossche-encyclopedie.nl"))
  }
  OPTIONAL{
    ?item wdt:P625 ?coords .
  }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```

### alle straatnaamvarianten die in Wikidata te vinden zijn

```
SELECT ?item ?itemLabel ?altlabel ?officiallabel ?namelabel ?openbruimteLabel WHERE {
  VALUES ?openbruimte { wd:Q79007 wd:Q174782 wd:Q574990 } # straat, plein, hofje
  ?item wdt:P31 ?openbruimte .
  ?item wdt:P131 wd:Q9807 .
  OPTIONAL{
    ?item skos:altLabel ?altlabel .
  }
  OPTIONAL{
    ?item wdt:P1448 ?officiallabel .
  }
  OPTIONAL{
    ?item wdt:P2561 ?namelabel .
  }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "nl,en". }
}
```


### straten zonder coördinaten (en met einddatum, indien aanwezig)

```
SELECT ?item ?itemLabel ?end WHERE {
  ?item wdt:P31 wd:Q79007 .
  ?item wdt:P131 wd:Q9807 .
  OPTIONAL{
    ?item wdt:P576 ?end .
  }
  MINUS{ ?item wdt:P625 ?coords }
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```