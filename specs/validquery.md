# Kwaliteitsinformatie

Dit document beschrijft het technische scenario voor het elektronisch opvragen van gegevens over de kwaliteit van de verpleeghuiszorg in een zorginstelling. Het is een van de scenario's binnen de afsprakenset voor keteninformatie kwaliteit verpleeghuiszorg \(KIK-V\). Met technisch wordt gerefereerd naar de technische laag uit het Europese interoperabiliteitsraamwerk \(EIF\). In het Nictiz interoperabiliteitsraamwerk zijn dit de lagen applicatie en IT-Infrastructuur.

De indicatoren voor het meten van de kwaliteit zijn vastgelegd in het kwaliteitskader verpleeghuiszorg. Het kwaliteitskader is opgesteld door de Kwaliteitsraad van het Zorginstituut Nederland en is daarna vastgesteld door de Raad van Bestuur van het Zorginstituut op 12 januari 2017.

## Gevalideerde vraag en antwoord

Een gevalideerde vraag is een vraag die gespecificeerd is in een uitwisselprofiel. De vragen zijn gevalideerd op rechtmatigheid, doelbinding en proportionaliteit voor de afnemer en aanbieder. Deze validatie wordt uitgevoerd onder de eindverantwoordelijkheid van een onafhankelijke beheerorganisatie. Voor een aanbieder is het verifieerbaar dat de vraag gevalideerd en geldig is.

De vraag om gegevens wordt gesteld vanuit een organisatie in de rol van afnemer. Deze rol kan bijvoorbeeld ingevuld worden door de Inspectie Gezondheidszorg en Jeugd \(IGJ\), Nederlandse Zorgautoriteit \(NZa\), Zorginstituut Nederland \(ZIN\), zoprgkantoren etc.

### **Randvoorwaarden**

Voordat het scenario kan starten moet aan de onderstaande voorwaarden zijn voldaan.

* Voor een software agent moet een did-document \(zie [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/)\) zijn gepubliceerd in het [verifieerbare dataregister](verifiabledataregister.md) \(VDR\) waardoor zij identificeerbaar is in het netwerk.
* Voor een datastation moet een [datacatalogus](datacatalogus.md) zijn gepubliceerd. De datacatalogus moet door een searchable resource geindexeerd kunnen worden voor de vindbaarheid van de data en de dataservices van de aanbieder.
* Het datastation moet een SPARQL-endpoint bevatten voor de uitvoering van de gevalideerde vraag.

### **Stappen in het scenario**

Het scenario start als het datatstation een bericht met een gevalideerde vraag \(hierna genoemd: vraag\) ontvangt van een consumer. De autorisatie is meegezonden met het bericht. Het bericht met de vraag en het antwoord van het datastation noemen we gezamenlijk de conversatie.

1. Het datastation logt het inkomende bericht \(in de Activity Log\) met het nummer dat de consumer heeft gegeven aan de conversatie.
2. Het datastation verifieert dat consumer geautoriseerd is \(zie scenario [autoriseren](autoriseer-consumer.md)\).
3. Het datastation verifieert dat de vraag een geldige gevalideerde vraag is.
4. Het datastation verwerkt de vraag door de SPARQL uit te voeren op een intern SPARQL-endpoint. Het resultaat is het antwoord op de vraag. Een aanbieder van de data mag interne processen hanteren voor de controle van het antwoord voordat zij het antwoord terugstuurt aan de consumer.
5. Het datastation antwoordt door een bericht aan te maken voor het resultaat van de SPARQL en deze te verzenden naar de consumer.
6. Het datastation logt het bericht met het antwoord \(in de Activity Log\) met het nummer van de conversatie.

Einde scenario.

### Alternatief scenario

Bij 2: de consumer is niet geautoriseerd voor het stellen van een vraag. Het datastation antwoord met de melding: HTTP/1.1 403 Forbidden.

Bij 3, de consumer heeft geen geldige gevalideerde vraag gepresenteerd. Het datastation antwoord met  een foutrapport waarin de melding is opgenomen dat of wel de presentatie dan wel de gevalideerde vraag niet geldig is.

### **Aanvullende eisen**

Het kanaal voor het transport van de berichten moet versleuteld zijn waarbij wederzijdse authenticatie is toegepast \(mTLS\).

## Realisatie van het scenario

### **Overzicht van de oplossing**

De gehanteerde softwarearchitectuur in onderstaand figuur is illustratief en geen eis.

![](diagrams/kikv-sequencediagram.svg)

De specificatie van de berichten is geinspireerd op [https://identity.foundation/didcomm-messaging/spec/](https://identity.foundation/didcomm-messaging/spec/). De huidige versie van deze specificatie is in ontwikkeling en zal vooralsnog niet worden toegepast.

In onderstaande specificatie zijn de berichten beschreven.

### **Gevalideerde vraag \(inkomend bericht\)**

#### **Envelop**

De vraag MOET verstuurd zijn in een envelop. De envelop is op de infrastructuurlaag een component waarmee berichtversleuteling kan worden toegepast voor het transport tussen software agents. De envelop geeft eveneens de mogelijkheid om tussenstations te gebruiken voor het routeren van berichten. het gebruik van een envelop voor messaging is een 'best practice', zie [https://www.enterpriseintegrationpatterns.com/patterns/messaging/EnvelopeWrapper.html](https://www.enterpriseintegrationpatterns.com/patterns/messaging/EnvelopeWrapper.html).

De envelop MAG NIET versleuteld zijn in de huidige versie van de afsprakenset. Alleen kanaalencryptie wordt toegepast.

De envelop MOET opgenomen zijn in de body van een HTTP request.

De envelop MOET als volgt zijn vormgegeven:

```json
"protected": {
   "alg": "none", 
   "typ": "JWM"
},
"payload": {
   "jti": "<unique identifier of the message, for example: urn:uuid:ef5a7369-f0b9-4143-a49d-2b9c7ee51117>",
   "type": "v1/basic-message/request",
   "from": "<did of holder>",
   "to": "<did of intended verifier>",
   "exp": "<expiration date>",
   "iat":"<issuance date>",
   "thread_id": "<identifier to associate the JWM to a group of related messages (conversation)>",
   "reply_url": "<url to which the response of the message can be sent>",
   "body": {
       "message": "<base64url-encoded JWT as string containing the verifiabble presentation>"
  }
},
"signature": ""
```

De claims jti, exp, iat MOETEN ingevuld zijn zoals beschreven in IETF RFC 7519 \(zie [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)\).

#### **Verifiable presentation**

De presentatie MOET een geldige verifiable presentation \(hierna prersentatie\) zijn zoals beschreven in [https://www.w3.org/TR/vc-data-model/](https://www.w3.org/TR/vc-data-model/). Het doel van de presentation is om de gevalideerde vraag te presenteren aan het datastation van een aanbieder waarbij de traceerbaarheid en integriteit van de presentatie gegarandeerd is.

De presentatie MOET elektronisch ondertekend zijn door de afnemer \(organisatieniveau\).

De presentatie MOET het formaat JSON Web Token als proof format gebruiken \(zie voorbeeld hieronder\). Indien Linked Data Proofs gebruikt kunnen worden, dan MOET dat in de metadata vana het datastation vermeld zijn.

De presentatie MOET als algortime 'EdDSA' hanteren. Indien andere algoritmes gebruikt kunnen worden, dan MOET dat in de metadata van het datastation vermeld zijn.

```json
"protected": {
   "alg": "EdDSA", 
   "kid": "<reference to assertion method in did document>",
   "typ": "JWT"
},
"payload": {
   "jti": "<presentation id>",
   "iss": "<did of holder>",
   "aud": "<did of intended verifier>",
   "nbf": "<valid from date, same as issuance date or later>",
   "exp": "<expiration date>",
   "iat": "<issuance date>",
   "vp": {
       "@context": ["https://www.w3.org/2018/credentials/v1"], 
       "type": ["VerifiablePresentation"], 
       "verifiableCredential": ["<base64url-encoded JWT as string>"]
    }
},
"signature": "<signature of the holder>"
```

#### **Verklaring gevalideerde vraag**

De verklaring MOET een geldige verifiable credential \(hierna credential\) zijn zoals beschreven in [https://www.w3.org/TR/vc-data-model/](https://www.w3.org/TR/vc-data-model/). Met de verklaring bewijst de afnemer dat de vraag een valide vraag is.

De verklaring MOET elektronisch ondertekend zijn door de beheerorganisatie \(organisatieniveau\).

De verklaring MOET het formaat JSON Web Token als proof format gebruiken \(zie voorbeeld hieronder\). Indien Linked Data Proofs gebruikt kunnen worden, dan MOET dat in de metadata vana het datastation vermeld zijn.

De verklaring MOET als algortime 'EdDSA' hanteren. Indien andere algoritmes gebruikt kunnen worden, dan MOET dat in de metadata van het datastation vermeld zijn.

De verklaring MOET een attribuut query bevatten die een geldige SPARQL-query als waarde bevat.

```json
"protected": {
    "alg": "EdDSA", 
    "kid": "<reference to assertion method in did document>",
    "typ": "JWT"
},
"payload": {
    "jti": "<credential id>",
    "iss": "<did of issuer>",
 	  "sub": "<did of holder>",
    "nbf": "<valid from date, same as issuance date or later>",
    "exp": "<expiration date>",
    "iat": "<issuance date>",
 	  "vc": {
        "@context": [
            "https://www.w3.org/2018/credentials/v1",
            "https://www.zinl.nl/2020/credentials/validatedquery/v1"
        ],
        "type": ["VerifiableCredential", "ValidQueryCredential"],
        "credentialSubject": {
            "dataset": "<dataset for which the query applies>",
            "query": "<SPARQL query>"
        }
    }
},
"signature":
```

#### **Status van de verklaring gevalideerde vraag**

In de verklaring MAG een attribuut opgenomen zijn met de verwijzing naar de status van de verklaring.

De wijze waarop het attribuut is opgenomen MOET voldoen aan de het verifiable credential data model \([https://www.w3.org/TR/vc-data-model/\#status](https://www.w3.org/TR/vc-data-model/#status)\). Als het attribuut is opgenomen, dan MOET de status van de verklaring geverifieerd zijn. Een geldige verklaring heeft de status 'valid'.

```json
"credentialStatus": {
    "id": "https://example.com/status/<status list identifier>",
    "type": "CredentialStatusList2017"
},
```

Het gehanteerde type is beschreven in: [https://w3c-ccg.github.io/vc-csl2017/](https://w3c-ccg.github.io/vc-csl2017/).

### **Antwoord op gevalideerde vraag \(uitgaand bericht\)**

#### **Envelop**

Het antwoord MOET verzonden zijn in een envelop.

De envelop MAG NIET zijn versleuteld in de huidige versie van de afsprakenset. Alleen kanaalencryptie wordt toegepast.

De envelop MOET opgenomen zijn in de body van een HTTP request.

De envelop MOET als volgt zijn vormgegeven:

```json
"protected": {
    "alg": "none", 
    "typ": "JWM"
},
"payload": {
    "jti": "<unique identifier of the message, for example: urn:uuid:ef5a7369-f0b9-4143-a49d-2b9c7ee51117>",
    "type": "v1/basic-message/response",
    "from": "<did of verifier>",
    "to": "<did of holder>",
    "exp": "<expiration date>",
    "iat":"<issuance date>",
    "thread_id": "<same identifier as used in inbound message>",
    "body": {
        "message": "<response>"
    }
},
"signature": ""
```

De claims jti, exp, iat MOETEN ingevuld zijn zoals beschreven in IETF RFC 7519 \(zie [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)\).

#### **Resultaatset**

De resultaat set MOET een key-value array zijn bestaande uit de identificatie van de verklaring en de het resultaat van de query \(zie hieronder\).

```json
"resultset": [
    {
        "id" : "<credential id>",
        "result" : "<query result>"
    }
]
```

### **Foutrapport \(uitgaand bericht\)**

De foutrapportage MOET verzonden zijn in een envelop.

De envelop MAG NIET versleuteld zijn in de huidige versie van de afsprakenset. Alleen kanaalencryptie wordt toegepast.

De envelop MOET opgenomen zijn in de body van een HTTP request.

De envelop MOET als volgt zijn vormgegeven:

```json
"protected": {
    "alg": "none", 
    "typ": "JWM"
},
"payload": {
    "id": "<unique identifier of the message, for example: urn:uuid:ef5a7369-f0b9-4143-a49d-2b9c7ee51117>",
    "type": "v1/error-report",
    "from": "<did of verifier>",
    "to": "<did of holder>",
    "exp": "<expiration date>",
    "iat":"<issuance date>",
    "thread_id": "<same identifier as used in inbound message>",
    "body": {
        "message": "<error message>"
    }
},
"signature": ""
```

De claims jti, exp, iat MOETEN ingevuld zijn zoals beschreven in IETF RFC 7519 \(zie [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)\).

