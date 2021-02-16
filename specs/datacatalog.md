# Datacatalogus

Voor een datastation moet beschreven zijn welke datasets beschikbaar zijn. De dataset worden daarom beschreven in metadata. De metadata moet overeenkomstig de FAIR-data principes doorzoekbaar zijn en vindbaar. Voor het beschrijven van de metadata wordt gebruik gemaakt van de DCAT als ontologie voor een datacatalogus.

Dit document geeft een voorbeeld van de minimale set van metadata voor een datastation. Voor een beschrijving van de attributen verwijzen we naar [https://www.w3.org/TR/vocab-dcat-2/](https://www.w3.org/TR/vocab-dcat-2/).

## De minimale beschrijving van de datasets

In onderstaand voorbeeld is een json-ld opgenomen van de metadata van een datastation. Dit voorbeeld kan als uitgangspunt worden genomen en gekopieerd worden naar een werkelijk datastation.

In het voorbeeld is gebruik gemaakt van Turtle \([https://www.w3.org/TR/turtle/](https://www.w3.org/TR/turtle/)\) als format voor het beschrijven van de metadata.

### Beschrijving van de namespaces

De datacatalogus gebruikt de onderstaande namespaces voor het beschrijven van het datastation.

```turtle
@prefix dcat: <http://www.w3.org/ns/dcat#> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix schema: <http://schema.org/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
```

### Beschrijving van de datacatalogus

De datacatalogus is een catalogus van datasets. Voor ieder datastation moet een datacatalogus zijn beschreven. Onderstaand voorbeeld bevat de minimale beschrijving van de datacatalogus.

```turtle
:cat2202
  a dcat:Catalog ;
  dct:title "Datacatalogus voorbeeldzorg"@nl ;
  dct:description "Een beschrijving van de datasets in het datastation van voorbeeldzorg"@nl ;
  dct:publisher :voorbeeldzorg ;
  dct:issued "2021-01-25"^^xsd:date ;
  dct:license <https://creativecommons.org/licenses/by/4.0/> ;
  dcat:dataset :ds2202-employees .
  
:voorbeeldzorg
  a foaf:Agent ;
  foaf:name "Voorbeeldzorg" ;
  @prefix dfcore: <http://ontology.tno.nl/dfcore#> .
  dct:type <http://purl.org/adms/publishertype/NonProfitOrganisation> .
```

### Beschrijving van de datasets

Iedere dataset in de datacatalogus is beschreven. Onderstaand voorbeeld bevat de minimale beschrijving van de dataset.

```turtle
:ds2202-employees
  a dcat:Dataset ;
  dct:title "Linked data personeel"@nl ;
  dct:description "Deze dataset bevat alle personeelsleden van voorbeeldzorg. "@nl ;
  dcat:keyword "Personeel" ;
  dct:publisher :voorbeeldzorg ;
  dct:issued "2021-01-25"^^xsd:date ;
  dct:conformsTo <http://purl.org/ozo/hr> ;
  dct:accrualPeriodicity <http://purl.org/cld/freq/daily> ;
  dcat:distribution :dist-validatedquery .
```

### Beschrijving van de distributie

Iedere distributie van de dataset is beschreven. Onderstaand voorbeeld bevat de minimale beschrijving van de datadistributie en de bijbehorende service voor gevalideerde vragen.

```turtle
:dist-validatequery
  a dcat:Distribution ;
  dcat:accessService :validatedquery-service ;
  dct:conformsTo <https://www.w3.org/TR/rdf-schema/> ;
  dct:title "RDF distributie voor kwaliteitsinformatie."@nl .
    
:validatedquery-service
  a dcat:DataService
  dct:conformsTo <https://verwijzing-naar-de-gepubliceerde-specificatie/> ;
  dct:title "Gevalideerde vragen"@nl ;
  dct:description "Service voor het uitvoeren van gevalideerde vragen via sparql"@nl ;
  dcat:endpointURL <http://data.example.com/api/validatedquery> .
```

