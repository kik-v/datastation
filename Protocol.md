## Protocol

### Hypertext Transfer Protocol (HTTP)

Een datastation moet gebruik maken van het HTTP protocol (zie https://tools.ietf.org/html/rfc2616) voor de communicatie met een afnemer en andere systeemactoren.

### Transport Layer Security (TLS)

Een datastation moet communicatie met wederzijdse authenticatie \(mTLS\) afdwingen. Voor de eigen authenticatie moet het datastation een PKIOverheid-certificaat gebruiken en ook alleen certificaten van PKIOverheid vertrouwen.

### Authenticatie (Open ID Connect)

Een datastation moet authenticatie uitvoeren met het scenario voor een Self-Issued OpenID Provider \(SIOP\), zie [https://openid.net/specs/openid-connect-core-1\_0.html\#SelfIssued](https://openid.net/specs/openid-connect-core-1_0.html#SelfIssued). Het is een scenario die gecombineerd moet worden met decentralized identifiers overeenkomstig de volgende specificatie: [https://identity.foundation/did-siop/](https://identity.foundation/did-siop/).

### Autorisatie (OAuth2)

Een datastation moet voor de autorisatie gebruik maken van OAuth2 (zie https://tools.ietf.org/html/rfc6749). Na authenticatie moet de afnemer autorisatie aanvragen voor een scope. De scope is een gegevensbron (een resource) of een service. Een aanbieder mag besluiten om een autorisatie automatisch te accepteren of een handmatig acceptatieproces uit te voeren. Resultaat van de autorisatie is een Access Token (overreenkomstig de OAuth2-standaard) waarmee de afnemer toegang verkrijgt tot de resource of service.

Een datastation moet voor de kwaliteitsinformatie verpleeghuiszorg de scope "verifiedsparql" gebruiken.

### Communicatie

Een datastation kan verschillende vormen van communicatie ondersteunen. Keteninformatie over de kwaliteit van verpleeghuiszorg is vooralsnog alleen beschikbaar via messages.

#### Messages

De specificatie voor asynchrone communicatie is geïnspireerd op [https://identity.foundation/didcomm-messaging/spec/](https://identity.foundation/didcomm-messaging/spec/).

Messages moeten verstuurd zijn in een envelop. De envelop is op de infrastructuurlaag een component waarmee berichtversleuteling kan worden toegepast voor het transport tussen software agents. Vooralsnog zal hiervoor echter mTLS worden toegepast omdat dit meer vertrouwen geeft bij de organisaties in de rol van verzender en ontvanger. Het is bestaande technologie die deze organisaties al gebruiken, kennen en herkennen. 

De envelop geeft de mogelijkheid om tussenstations te gebruiken voor het routeren van berichten. Het gebruik van een envelop voor messaging is een vorm van goed gebruik, zie [https://www.enterpriseintegrationpatterns.com/patterns/messaging/EnvelopeWrapper.html](https://www.enterpriseintegrationpatterns.com/patterns/messaging/EnvelopeWrapper.html).

De envelop moet niet versleuteld zijn. Alleen kanaalencryptie wordt toegepast.

De envelop moet opgenomen zijn in de body van een HTTP request.

Het type bericht voor een request moet <code>v1/basic-message/request</code> zijn.

De envelop voor een request moet zijn vormgegeven zoals in onderstaand voorbeeld. 

De envelop voor de response is identiek aan de envelop voor de request met uitzondering van het type en de reply_url. Het type voor een response is <code>v1/basic-message/response</code>. Een response bevat geen reply_url.

De envelop voor het foutrapport is identiek aan de envelop voor de request met uitzondering van het type, de reply_url en de message. Het type voor een foutrapport is <code>v1/error-report</code>. Een foutrapport bevat geen reply_url. De message moet de melding van de fout in plain text beschrijven.

<pre class='example nohighlight'>
"protected": {
   "alg": "none", 
   "typ": "JWM"
},
"payload": {
   "jti": "unieke identificatie van het bericht, bijvoorbeeld: urn:uuid:ef5a7369-f0b9-4143-a49d-2b9c7ee51117>",
   "type": "v1/basic-message/request",
   "from": "did van de verzender",
   "to": "did van de geadresseerde",
   "exp": "expiratie datum (optioneel)",
   "iat":"datum van aanmaak (optioneel)",
   "thread_id": "identifier om de JWM te associëren aan de conversatie",
   "reply_url": "url waar de response naar toe kan worden verzonden",
   "body": {
       "message": "base64url-encoded JWT string met de inhoud van het bericht"
  }
},
"signature": ""
</pre>

De claims jti, exp, iat moeten zijn ingevuld zoals beschreven in IETF RFC 7519 \(zie [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)\).


