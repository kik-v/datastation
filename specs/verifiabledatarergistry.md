# Verifieerbaar dataregister

Het verifieerbare register \(Engels: verifiable data registry\) is de verzamelnaam voor een component met een aantal generieke functies. Hieronder zijn deze generieke functies benoemd en de manier waarop ze zijn ingevuld:

**Publicatie van DID Documents** zoals bedoeld in [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/). De DID moet een instantie zijn van een van de volgende DID Methods:

* did:nuts Method Specification, zie \(@@TODO: referentie opnemen naar specificatie\)
* did:web Method Specification \([https://w3c-ccg.github.io/did-method-web/](https://w3c-ccg.github.io/did-method-web/)\). De DID en het DID document is in de web method gekoppeld aan de domeinnaam van een organisatie. Eis is dat de twee software agents verbonden moeten zijn met mutualTLS waarbij een software agent moet verifiëren dat de domeinnaam van de DID overeenkomst met de domeinnaam in het PKI certificaat.

**Publicatie van de lijst met vertrouwde uitgevers van verklaringen** \(issuer trust list\). Vooralsnog zal deze lijst handmatig worden gepubliceerd.

**Publicatie van het schema met attributen in de verklaring**. Dit is niet geïmplementeerd.

**Publicatie van de status van een verklaring.** De uitgever mag de lijst publiceren. Dit overeenkomstig het verifiable credential data model \([https://www.w3.org/TR/vc-data-model/\#status](https://www.w3.org/TR/vc-data-model/#status)\).

