## Services

### Kwaliteitsinformatie verpleeghuiszorg

Het verkrijgen van kwaliteitsinformatie wordt uitgevoerd door het stellen van een gevalideerde vraag (hierna vraag genoemd). Deze vraag is gespecificeerd in een uitwisselprofiel. De vragen zijn gevalideerd op rechtmatigheid, doelbinding en proportionaliteit voor de afnemer en aanbieder. De validatie wordt door een onafhankelijke beheerorganisatie. Voor een aanbieder is het verifieerbaar dat de vraag gevalideerd en geldig is.

De vraag om gegevens wordt gesteld vanuit een organisatie in de rol van afnemer. Deze rol kan bijvoorbeeld ingevuld worden door de Inspectie Gezondheidszorg en Jeugd \(IGJ\), Nederlandse Zorgautoriteit \(NZa\), Zorginstituut Nederland \(ZIN\), zorgkantoren etc.

| Stappen in het hoofdscenario                                 |
| ------------------------------------------------------------ |
| 1. De afnemer presenteert een vraag.            |
| 2. Het datastation neemt de vraag in ontvangst. |
| 3. Het datastation logt de ontvangst.                        |
| 4. Het datastation verifieert dat de afnemer is geautoriseerd. |
| 5. Het datastation verifieert dat de presentatie geldig is.  |
| 6. Het datastation verifieert dat de vraag geldig is. |
| 7. Het datastation behandelt de vraag en stelt het antwoord samen. |
| 8. Het datastation verzendt het antwoord aan de afnemer.     |
| 9. Het datastation logt het uitgaande antwoord.              |
| Einde scenario.                                              |
| --                                                           |
| **Alternatief scenario**                                     |
| 4a, als de afnemer niet is geautoriseerd. Het datastation antwoord dat afnemer niet geautoriseerd is. |
| 5a. als de presentatie niet geldig is. Het datastation stelt een foutrapport samen en vervolgt bij 8. |
| 6a. als de vraag niet geldig is. Het datastation stelt een foutrapport samen en vervolgt bij 8. |

In de onderstaande paragrafen worden een aantal van de stappen verder gespecificeerd.

#### Loggen

Het datastation moet de vragen en anwoorden loggen in de activiteiten log. Hiermee moet een aanbieder de behandeling van de vraag kunnen verantwoorden. Voor meer informatie, zie het hoofdstuk [Activiteiten Log](#activiteiten-log).

#### Autorisatie

Het datastation verifieert dat het Access Token geldig is. Het Access Token is door het datastation uitgegeven overeenkomstig het OAuth2 protocol.
Als het token niet geldig is, dan antwoord het datastation met de melding: HTTP/1.1 403 Forbidden. Anders met HTTP/1.1 200 OK.

Voor meer informatie, zie hoofdstuk [Autorisatie (OAuth2)](#autorisatie-oauth2).

#### Presentatie

Door middel van de verificatie van de presentatie kan de herkomst van de vraag getraceerd worden naar de afnemer. 

De identificatie van de afnemer moet geregistreerd zijn in de vertrouwensinfrastructuur van het ecosysteem (zie hoofdstuk [Vertrouwen](#vertrouwen).

De presentatie moet een geldige verifiable presentation \(hierna prersentatie\) zijn zoals beschreven in [https://www.w3.org/TR/vc-data-model/](https://www.w3.org/TR/vc-data-model/).

De presentatie moet elektronisch ondertekend zijn door de afnemer \(organisatieniveau\).

<pre class='example nohighlight'>
"protected": {
   "alg": "EdDSA", 
   "kid": "referentie naar de assertion method in het did document",
   "typ": "JWT"
},
"payload": {
   "jti": "een unieke presentation id",
   "iss": "de did van de afnemer",
   "aud": "de did van de aanbieder",
   "nbf": "datum vanaf geldig",
   "exp": "datum t/m geldig",
   "iat": "datum uitgifte",
   "vp": {
       "@context": ["https://www.w3.org/2018/credentials/v1"], 
       "type": ["VerifiablePresentation"], 
       "verifiableCredential": ["base64url-encoded JWT as string"]
    }
},
"signature": "elektronische handtekening van de afnemer"
</pre>

#### Vraagbehandeling

De vraag moet een geldige verifiable credential \(hierna credential\) zijn zoals beschreven in [https://www.w3.org/TR/vc-data-model/](https://www.w3.org/TR/vc-data-model/). Met de verklaring bewijst de afnemer dat de vraag een valide vraag is.

De vraag moet elektronisch ondertekend zijn door de beheerorganisatie \(organisatieniveau\).

De vraag moet een attribuut profile bevatten die een verwijzing naar het uitwisselprofiel bevat. De verwijzing is bij voorkeur een linked data verwijzing naar de specificatie van het uitwisselprofiel.

De vraag moet een attribuut ontology bevatten die een geldige waarde bevat van de gehanteerde domeinontologie in het uitwisselprofiel.

De vraag moet een attribuut query bevatten die een geldige SPARQL-query als waarde bevat.

<pre class='example nohighlight'>
"protected": {
    "alg": "EdDSA", 
    "kid": "referentie naar de assertion method in het did document",
    "typ": "JWT"
},
"payload": {
    "jti": "een unieke globale identificatie voor de vraag",
    "iss": "de did van de beheerorganisatie",
 	  "sub": "de did van de afnemer",
    "nbf": "datum vanaf geldig",
    "exp": "datum t/m geldig",
    "iat": "datum uitgifte",
 	  "vc": {
        "@context": [
            "https://www.w3.org/2018/credentials/v1",
            "https://www.zinl.nl/2020/credentials/validatedquery/v1"
        ],
        "type": ["VerifiableCredential", "ValidQueryCredential"],
        "credentialSubject": {
            "profile": "(linked data) verwijzing naar het uitwisselprofiel"
            "ontology": "gehanteerde ontologie",
            "query": "de SPARQL query die moet worden uitgevoerd"
        }
    }
},
"signature": "elektronische handtekening van de beheerorganisatie"
</pre>

#### Status van de vraag

In de vraag mag een attribuut opgenomen zijn met de verwijzing naar de status van de vraag.

De wijze waarop het attribuut is opgenomen moet voldoen aan de het verifiable credential data model \([https://www.w3.org/TR/vc-data-model/\#status](https://www.w3.org/TR/vc-data-model/#status)\). Als het attribuut is opgenomen, dan moet de status van de vraag geverifieerd zijn. Een geldige vraag heeft de status 'valid'.

<pre class='example nohighlight'>
"credentialStatus": {
    "id": "https://example.com/status/statuslijst-identifier",
    "type": "CredentialStatusList2017"
},
</pre>

Het gehanteerde type is beschreven in: [https://w3c-ccg.github.io/vc-csl2017/](https://w3c-ccg.github.io/vc-csl2017/).

#### Samenstellen antwoord

Een geldige vraag moet door een afnemer in behandeling worden genomen. De aanbieder mag de vraag geautomatiseerd beantwoorden of een handmatig vrijgaveproces hanteren voor het beantwoorden van de vraag.

Voor het samenstellen van het antwoord moet de vraag (de SPARQL) gesteld worden. De aanbieder beantwoord de vraag op basis van het kennismodel (de ontologie) en de kennis (de informatie) die zij geregistreerd heeft. 

Het antwoord (van de SPARQL) moet opgenomen zijn in een key-value array bestaande uit de identificatie van de vraag en het resultaat van de query \(zie hieronder\).

<pre class='example nohighlight'>
"resultset": [
    {
        "id" : "de unieke identificatie van de vraag",
        "result" : "query resultaat"
    }
]
</pre>
