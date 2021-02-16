# Autorisatie

Door middel van autorisatie kan een aanbieder de gevalideerde vragen van een afnemer autoriseren. Het is een aanvullende maatregel om afnemers te accepteren als betrouwbare organisatie. Als een afnemer niet langer vertrouwt is, dan kan een aanbieder de autorisatie intrekken en daarmee de toegang tot het stellen van gevalideerde vragen ontnemen.

## Realisatie van het scenario

Dit document beschrijft het scenario voor het autoriseren van een consumer. Het scenario is een scenario waarbij de client een Self-Issued OpenID Provider \(SIOP\) gebruikt, zie [https://openid.net/specs/openid-connect-core-1\_0.html\#SelfIssued](https://openid.net/specs/openid-connect-core-1_0.html#SelfIssued).

De beschrijving voor het gebruik van OpenID Connect in combinatie met decentralized identifiers is opgenomen in de volgende specificatie: [https://identity.foundation/did-siop/](https://identity.foundation/did-siop/).

De hierboven genoemde specificaties zijn in onderstaand diagram toegepast voor het verkrijgen van kwaliteitsinformatie van een verpleeghuis. 

![](diagrams/kikv-register.svg)

Met het onderstaande voorbeeld verzoekt de consumer om autorisatie aan het datastation.

```http
POST /oauth2/authorize HTTP1/1
Host: https://datastation.example.com

response_type=code
&client_id=did:nuts:some-unique-id-from-nuts
&redirect_uri=https://consumer.example.com/oauth2/callback
&scope=openid did_authn validatedquery
&state=this-should-be-some-generated-secret-token
```

De resource owner reageert hierop met een redirect naar de autorisatieserver voor authenticatie van de consumer. De autorisatieserver stuurt een authenticatievezoek naar de Self-Issued OpenID Connect Provider \(SIOP\). Resultaat van het autorisatieverzoek is een Access Token.

Een aanbieder mag besluiten om een autorisatie automatisch te accepteren of een handmatig acceptatieproces te voeren.







