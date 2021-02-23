## Platform

De platformlaag is een laag met generieke functionaliteit voor een aantal aspecten. Ieder aspect is als paragraaf van dit hoofdstuk opgenomen en beschrijft de eisen voor dat aspect. 

### Vertrouwen

Een datastation is onderdeel van een ecosysteem, een ecosysteem van deelnemers waarin vertrouwen een belangrijke rol speelt. In het ecosysteem onderkennen we de rol van vertrouwensleverancier om de middelen voor dat vertrouwen te leveren. De vertrouwensleveranciers vormen gezamenlijk een vertrouwensinfrastructuur. Deze vertrouwensinfrastructuur is gebaseerd op de standaarden van W3C.

In de vertrouwensinfrastructuur worden de volgende onderdelen gepubliceerd:

- **Publicatie van DID Documents** zoals bedoeld in [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/).
- **Publicatie van de lijst met vertrouwde uitgevers van verklaringen** \(issuer trust list\). Vooralsnog zal deze lijst handmatig worden gepubliceerd.
- **Publicatie van het schema met attributen in de verklaring**. Dit is vooralsnog niet geïmplementeerd.
- **Publicatie van de status van een verklaring** waarbij vooralsnog wordt aangenomen dat de uitgever de lijst zelf publiceert. Dit overeenkomstig het verifiable credential data model \([https://www.w3.org/TR/vc-data-model/\#status](https://www.w3.org/TR/vc-data-model/#status)\).

Een datastation moet gebruik kunnen maken van de vertrouwensinfrastructuur om afnemers en andere actoren in het ecosysteem te kunnen authenticeren en verklaringen te kunnen verifiëren. Een datastation moet daarom de rol van verifeerder (Engels: verifyer) kunnen uitvoeren overeenkomstig de volgende specificatie: [https://www.w3.org/TR/vc-use-cases/](https://www.w3.org/TR/vc-use-cases/) 

Een datastation moet decentrale identificaties (DIDs) van actoren ondersteunen overeenkomstig de volgende specificatie: [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/). Binnen deze specificatie moet een datastation minimaal ondersteuning geven aan:

- De specificatie van JSON Web Keys (JWK) als key format (verification method type is gelijk aan JsonWebKey2020). Zie ook: https://www.w3.org/TR/did-spec-registries/.
- Private en publieke sleutels van het type OKP met het algoritme EdDSA.

Een datastation moet haar eigen decentrale identificaties (DIDs) en private sleutels veilig opslaan.

Een datastation moet minimaal de Nuts methode \(did:nuts, zie @@TODO: referentie opnemen naar specificatie\) ondersteunen als DID methode. 

### Activiteiten Log 

Het datastation moet alle activiteiten loggen met het nummer dat de afnemer heeft gegeven aan de conversatie. Met de log moet een aanbieder de behandeling van een vraag kunnen verantwoorden. 

Voor iedere activiteit moet de thread_id (het conversatienummer) geregistreerd zijn, de datum en tijd van de activiteit, een omschrijving van de activiteit en de inhoud van het bericht (request of response).

### Datacatalogus

Een datastation moet beschrijven welke gegevensverzamelingen beschikbaar zijn. Dit moet beschreven zijn in een datacatalogus. Wanneer gesproken wordt over de metadata van een datastation, dan wordt de datacatalogus bedoeld. De datacatalogus moet overeenkomstig de FAIR-data principes doorzoekbaar en vindbaar zijn. Dit laatste is niet een verantwoordelijkheid voor het datastation zelf. Het datastation moet alleen zorgen dat de metadata op een vast punt beschikbaar is, om vervolgens door een gegevensgids geïndexeerd te kunnen worden. De gegevensgids is de rol in het ecosysteem die een datacatalogus doorzoekbaar maakt en de data in een datastation vindbaar maakt.

Een datastation moet voor het beschrijven van de metadata gebruik maken van DCAT als ontologie voor een datacatalogus. Voor de beschrijving van DCAT en de attributen in deze ontologie verwijzen we naar [https://www.w3.org/TR/vocab-dcat-2/](https://www.w3.org/TR/vocab-dcat-2/).

Een datastation moet haar datacatalogus publiceren onder het volgende relatieve pad onder haar domeinnaam met subdomein data: <code>/datacatalog</code>. 

<pre class='example'>
GET /datacatalog HTTP1/1
Host: https://data.example.com
</pre>

In onderstaande voorbeelden is de minimale set van een datacatalogus voor een datastation weergegeven. In het voorbeeld is gebruik gemaakt van Turtle \([https://www.w3.org/TR/turtle/](https://www.w3.org/TR/turtle/)\) als format voor het beschrijven van de datacatalogus.

Een datacatalogus maakt gebruik van namespaces voor het beschrijven van het datastation. Een namespace verwijst naar de ontologie die gebruikt wordt voor het beschrijven van de attributen in de datacatalogus.

<pre class='example nohighlight'>
@prefix dcat: &#x3C;http://www.w3.org/ns/dcat##&#x3E; .
@prefix dct: &#x3C;http://purl.org/dc/terms/#&#x3E; .
@prefix foaf: &#x3C;http://xmlns.com/foaf/0.1/#&#x3E; .
@prefix schema: &#x3C;http://schema.org/#&#x3E; .
@prefix xsd: &#x3C;http://www.w3.org/2001/XMLSchema##&#x3E; .
@prefix rdfs: &#x3C;http://www.w3.org/2000/01/rdf-schema##&#x3E; .
@prefix dfcore: &#x3C;http://ontology.tno.nl/dfcore#&#x3E; .
</pre>

Als eerste stap moet de datacatalogus zelf beschreven zijn en de organisatie die de gegevensverzamelingen publiceert. Onderstaand voorbeeld beschrijft de datacatalogus. Voor de organisatie moet ook de identificatie van deze organisatie opgenomen zijn, de identificatie waarmee de organisatie in het ecosysteem geïdentificeerd kan worden.

<pre class='example nohighlight'>
:cat2202
  a dcat:Catalog ;
  dct:title "Datacatalogus voorbeeldzorg"@nl ;
  dct:description "Een beschrijving van de datasets in het datastation van voorbeeldzorg"@nl ;
  dct:publisher :voorbeeldzorg ;
  dct:issued "2021-01-25"^^xsd:date ;
  dct:license &#x3C;https://creativecommons.org/licenses/by/4.0/&#x3E; ;
  dcat:dataset :ds2202-employees .

:voorbeeldzorg
  a foaf:Agent ;
  foaf:name "Voorbeeldzorg" ;
  dfcore:hasBusinessRegisterNumber "20091741" ;
  dct:type &#x3C;http://purl.org/adms/publishertype/NonProfitOrganisation&#x3E; .
</pre>

De datacatalogus is een catalogus van datasets. Iedere dataset moet in de datacatalogus zijn beschreven.

<pre class='example nohighlight'>
:ds2202-employees
  a dcat:Dataset ;
  dct:title "Linked data personeel"@nl ;
  dct:description "Deze dataset bevat alle personeelsleden van voorbeeldzorg. "@nl ;
  dcat:keyword "Personeel" ;
  dct:publisher :voorbeeldzorg ;
  dct:issued "2021-01-25"^^xsd:date ;
  dct:conformsTo &#x3C;http://purl.org/ozo/hr&#x3E; ;
  dct:accrualPeriodicity &#x3C;http://purl.org/cld/freq/daily&#x3E; ;
  dcat:distribution :dist-validatedquery .
</pre>

Iedere distributie van de dataset moet zijn beschreven. Voor de kwaliteitsinformatie moet een service zijn opgenomen voor <code>verifiedsparql-service</code>.

<pre class='example nohighlight'>
:dist-validatequery
  a dcat:Distribution ;
  dcat:accessService :verifiedsparql-service ;
  dct:conformsTo &#x3C;https://www.w3.org/TR/rdf-schema/&#x3E; ;
  dct:title "RDF distributie voor kwaliteitsinformatie."@nl .

:verifiedsparql-service
  a dcat:DataService
  dct:conformsTo &#x3C;https://verwijzing-naar-de-gepubliceerde-specificatie/&#x3E; ;
  dct:title "Gevalideerde vragen"@nl ;
  dct:description "Service voor het uitvoeren van gevalideerde vragen via sparql"@nl ;
  dcat:endpointURL &#x3C;http://data.example.com/api/verifiedsparql&#x3E; .
</pre>



