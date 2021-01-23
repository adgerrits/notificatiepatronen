[Home](index.md)

- [Inleiding](#inleiding)
- [Rollen en samenwerking](#rollen-en-samenwerking)
  - [(Gebeurtenis)producent](#gebeurtenisproducent)
  - [Makelaar](#makelaar)
  - [(Nofificatie)consument](#nofificatieconsument)
  - [Samenwerking](#samenwerking)
- [Niet-inhoudelijke aspecten](#niet-inhoudelijke-aspecten)
  - [Synchroon en a-synchrone communicatie](#synchroon-en-a-synchrone-communicatie)
  - [Push en pull](#push-en-pull)
  - [veilig of onveilig](#veilig-of-onveilig)
  - [Wel of niet gegarandeerde aflevering](#wel-of-niet-gegarandeerde-aflevering)
  - [1- of 2-weg communicatie](#1--of-2-weg-communicatie)
  - [Signalen of replicatie](#signalen-of-replicatie)
  - [Met zonder gegevens](#met-zonder-gegevens)
  - [Actueel / historisch](#actueel--historisch)
  - [Systeemevents / Businessevents](#systeemevents--businessevents)
  - [formeel / informeel](#formeel--informeel)
  - [informatierijk / informatiearm](#informatierijk--informatiearm)
  - [Fire-and-forget of request-response](#fire-and-forget-of-request-response)
- [Integratie stijlen algemeen](#integratie-stijlen-algemeen)
- [Berichtuitwisseling als integratiestijl](#berichtuitwisseling-als-integratiestijl)
- [Patronen](#patronen)
  - [Integratie patronen](#integratie-patronen)
  - [Conversatie / interactie patronen](#conversatie--interactie-patronen)
  - [Softwareontwikkel patronen](#softwareontwikkel-patronen)
    - [Observer patroon](#observer-patroon)
      - [Webhooks](#webhooks)
    - [Mediator pattern](#mediator-pattern)
      - [Publish subscribe pattern](#publish-subscribe-pattern)
  - [Patronen specifiek voor notificeren](#patronen-specifiek-voor-notificeren)
    - [Event-Notification](#event-notification)
    - [Event-Carried State Transfer](#event-carried-state-transfer)
    - [Event-sourcing](#event-sourcing)
- [Protocollen](#protocollen)
  - [REST](#rest)
  - [AMQP](#amqp)
  - [Overige (?)](#overige-)
- [Inrichting scenarios](#inrichting-scenarios)

plandatum overschreden
zaken waarbij besluit

# Inleiding

Er zijn veel manieren waarop notificeren is te realiseren. Wat de meest geschikte manier is hangt af van de context. Er is daarom niet één manier die het beste is. Net zoals bij andere ontwerpuitdagingen zijn er wel 'patronen' te onderkennen die oplossingen voor vergelijkbare ontwerpuitdagingen beschrijven. Met behulp daarvan kan binnen een bepaalde context tot een oplossing worden gekomen die maximaal gebruik maakt van bestaande kennis en ervaring.
Hierna worden verschillende soorten patronen beschreven. Om binnen een bepaalde context te kunnen bepalen welke patronen, in welke mate, bruikbaar zijn moet worden beoordeeld of ze passen bij wat binnen de context nodig is. Daarbij spelen een aantal niet-inhoudelijke ('non-functional) aspecten een rol. Hierna geven we daarom eerst een korte beschrijving van een aantal van die terugkerende niet-inhoudelijke aspecten.

@@@Event driven architecture:

- 1 weg communicatie ('fire and forget' ipv 'request reply)
- communiceren van domein-events
- 1 of meer ontvangers
- Iedere ontvangers bepaalt zelf wat ze doen met notificatie
- Gegarandeerde aflevering? Oude events?

Events + REST = feed :

- eenvoudig, pull
- ontvanger abonneert zelf op feed
- ontvanger pollt zelf
-
-

# Rollen en samenwerking

Bij notificeren zoals het binnen de context van dit project zijn meerdere partijen betrokken. Het is belangrijk om helder te hebben wie wat moet doen binnen notificatieprocessen. We maken gebruik van 'rollen' om dit te beschrijven. Bij gebruik van verschillende patronen kunnen rollen op verschillende manieren door betrokken systemen worden ingevuld.
In de praktijk worden verschillende termen gebruikt om vergelijkbare rollen mee aan te duiden. Welke term het meest geschikt is hangt af van de context en het abstractieniveau.
Hieronder beschrijven we drie rollen die we onderscheiden bij notificeren in zo neutraal mogelijke termen. In plaats van 'een systeem met de rol van' zal omwille van de leesbaarheid vaak alleen de rol worden benoemd ('de producent levert gegevens' in plaats van 'een systeem met de rol van producent levert gegevens').

## (Gebeurtenis)producent

Een systeem met de rol van 'gebeurtenisproducent', afgekort tot 'producent', is verantwoordelijk voor het produceren van beschrijvende gegevens over bepaalde plaatsgevonden gebeurtenissen ('gebeurtenisgegevens').
Hoe het dit doet kan verschillen. Het kan bijv. gegevens in de vorm van een bericht versturen of gegevens op een plaats opslaan waar andere systemen toegang toe hebben.
De rol van producent is níet verantwoordelijk voor het notificeren van andere partijen.
Termen die in andere contexten gebruikt worden voor deze rol zijn bijv. producer, subject, publisher, sender en dienstaanbieder.

## Makelaar

Een systeem met de rol van 'makelaar' is verantwoordelijk voor het verwerken van beschikbare gebeurtenisgegevens tot notificatiegegevens.
Termen die in andere contexten gebruikt worden voor deze rol zijn bijv. changemanager, (message)broker.

## (Nofificatie)consument

Een systeem met de rol van 'notificatieconsument', afgekort tot 'consument', is verantwoordelijk voor het verwerken van beschikbaar gestelde notificatiegegevens.
Termen die in andere contexten gebruikt worden voor deze rol zijn bijv. consumer, observer, receiver, subscriber, client.

## Samenwerking

Bij notificatieprocessen stelt de producent gebeurtenisgegevens beschikbaar aan de makelaar. De makelaar stelt op basis van deze gebeurtenisgegevens notificatiegegevens beschikbaar aan consumenten.
Bij sommige patronen vervult 1 systeem zowel de rol van producent als makelaar. Bij andere patronen zijn de rollen van producent en makelaar gescheiden. In het eerste geval communiceert het ene systeem direct met consumenten. In het tweede geval is dit niet zo, maar verloopt communicatie indirect via het systeem in de rol van makelaar.

# Niet-inhoudelijke aspecten

## Synchroon en a-synchrone communicatie

Systemen kunnen voor notificatie gebruik maken van synchrone communicatie. Bijvoorbeeld als het systeem dat notificeert meteen wil weten of het genotificeerde systeem de notificatie heeft ontvangen en/of goed heeft verwerkt. In lijn met het streven naar losse koppeling van systemen verdient a-synchrone communicatie voor notificeren normaal gesproken de voorkeur. Betrokken systemen kunnen daarbij zelf bepalen wanneer zij hun taken uitvoeren zonder rekening te hoeven houden met andere betrokken partijen.

---

**We gebruiken bij voorkeur a-syncrhone communicatie voor notificeren.**

---

## Push en pull

Notificaties kunnen door het notificerende systeem richting afnemende systemen worden gebracht ('push'). Bijv. als afnemende systemen zo snel mogelijk genotificeerd willen worden. Afnemende systemen kunnen ook zelf beschikbaar gestelde notificaties gaan ophalen ('pull'). Bijv. omdat ze zelf het moment van verwerken willen bepalen of omdat er van hele grote notificatieberichten sprake is. Tenslotte kan er ook een combinatie van beide mechanismen worden gebruikt. Bijv. door eerst een klein notificatiebericht te sturen ('push') met de melding dat er een groot notificatiebericht kan worden opgehaald ('pull'). Er is geen duidelijke voorkeur welk(e) (combinatie van) mechanismes toegepast worden voor notificeren.
Opmerking: In plaats van 'push of pull' wordt soms ook gesproken over 'request-response of event-based' notificeren.
Opmerking: het eerder geformuleerde uitgangspunt dat het notificerende systeem zo snel mogelijk nadat een gebeurtenis heeft plaatsgevonden de notificatie daarvan beschikbaar stelt is bij toepassing van beide mechanismen van belang.

## veilig of onveilig

## Wel of niet gegarandeerde aflevering

Het kan voor zowel het notificerende, maar vooral voor ontvangende systemen van belang zijn, dat notificaties 'gegarandeerd' worden ontvangen. Bij uitwisseling van gegevens tussen overheidsorganisaties zal gegarandeerde aflevering regelmatig nodig zijn. Omdat dit niet eenvoudig is te realiseren zal daarbij vaak gebruik worden gemaakt van gespecialiseerde ondersteunende standaarden (bijv. Digikoppeling) of en systemen (bijv. 'message-oriented middleware').
Opmerking: bij begrippen als 'gegarandeerd' en 'niet-gegarandeerd' geldt dat de acceptabele hoeveel onzekerheid contextafhankelijk is. Op basis van een uitgevoerde kosten/baten analyse kan bijv. besloten worden om te kiezen voor een oplossing waarbij aflevering niet, deels of volledig is gegarandeerd.

---

**We gebruiken bij aflevering van notificaties bij voorkeur gegarandeerde aflevering.**

---

## 1- of 2-weg communicatie

In veel gevallen hoeft het notificerende systeem niet te weten welke afnemers er zijn en wat zij met notificaties doen. Dan volstaat 1-weg communicatie. Idealiter is het notificerende systeem dan klaar als het een notificatie beschikbaar heeft gesteld ('fire and forget').
Soms is het echter nodig dat het notificerende systeem een reactie van krijgt van het genotificeerde systeem. Bijv. om te horen of een verwerking naar aanleiding van een notificatie (bijv. een betaling) wel of niet succesvol is uitgevoerd. In die gevallen zal een oplossing 2-weg verkeer moeten ondersteunen. Als het genotificeerde systeem meteen kan reageren kan synchrone communicatie volstaan. Als synchrone communicatie niet voldoet, bijv. om het lang duurt voor een reactie is te geven, moet asynchroon worden gecommuniceerd. Bijv. door via een afzonderlijk kanaal te reageren en via een eerder ontvangen correlatie-id aan te geven waarop de reactie betrekking heeft.

Opmerking: het feit dat een afnemer een statuscode terugstuurt naar aanleiding van een ontvangen bericht zien we binnen deze context niet als 2-weg communicatie.

## Signalen of replicatie

## Met zonder gegevens

## Actueel / historisch

## Systeemevents / Businessevents

## formeel / informeel

## informatierijk / informatiearm

@@@

## Fire-and-forget of request-response

Response kan synchroon ('meteen') of asynchroon ('later') zijn. Als asynchroon dan via callback of via queue-mechanisme met gebruik van een correlatie-id.
Fireandforget of one-way message

# Integratie stijlen algemeen

Notificeren is een mechanisme om integratie tussen applicaties te realiseren door op een afgesproken manier gegevens met elkaar uit te wisselen. We onderscheiden 4 soorten integratiestijlen die in meer of minder mate geschikt zijn voor realisatie van oplossingen voor notificeren[^1]

1. Gedeelde database ('Shared database'): Applicaties delen een database met gemeenschappelijk te gebruiken gegevens.
2. Bestandsuitwisseling ('File transfer'): Applicaties produceren bestanden met gegevens die door andere applicaties worden ingelezen.
3. Externe applicatiefunctionaliteit aanroep ('Remote Procedure Invocation'): Applicaties roepen elkaar via beschikbare interfaces aan om gegevens uit te wisselen.
4. Berichtuitwisseling ('Messaging'): Applicaties wisselen gegevens met elkaar uit via berichten.

Alle vier de integratiestijlen kunnen gebruikt worden om oplossingen voor notificeren mee te realiseren. Ze zijn echter niet allemaal even geschikt en ook niet in alle situaties bruikbaar. Binnen de context van overheidsbreed notificeren geldt voor de verschillende stijlen:

- 'Gedeelde database' stijl is niet bruikbaar omdat betrokken applicaties daarvoor toegang moeten hebben tot dezelfde database. Iets dat vaak onwenselijk of onmogelijk is, zeker als er verschillende organisaties bij betrokken zijn. We laten deze stijl daarom verder buiten beschouwing.
- 'Bestandsuitwisseling' is bruikbaar als er het niet nodig is om snel na het optreden van gebeurtenissen te notificeren en/of als het gaat de uitwisseling van grote hoeveelheden gegevens. In lijn met het uitgangspunt dat we gegevens zoveel mogelijk bevragen bij bronregistraties en onnodig dupliceren van gegevens willen voorkomen is deze stijl voor de meeste situaties onwenselijk.
- 'Externe applicatiefunctionaliteit aanroep' is bruikbaar om te notificeren maar heeft als groot nadeel dat er ongewenste afhankelijkheid tussen applicaties ontstaat. Zo moet de aangeroepen applicatie beschikbaar zijn op het moment van aanroep door de andere applicatie om notificatie succesvol te laten zijn.
- 'Berichtuitwisseling' is voor de meeste situaties de meest wenselijke stijl voor integratieoplossingen, ook voor oplossingen om notificatieprocessen in te richten. Hierna wordt beschreven wat de kenmerken van 'berichtuitwisseling' zijn.

# Berichtuitwisseling als integratiestijl

Bij 'berichtuitwisseling' worden uit te wisselen gegevens in zelfstandig te verwerken berichten opgeslagen. Het gebruik van berichten als atomaire eenheid van verwerking maakt het (beter) mogelijk om:

- berichten zelfstandig te kunnen transporteren, bewaren en verwerken
- asynchroon te communiceren
- applicaties onafhankelijk van elkaar te laten functioneren
- specialistische functionaliteit bij specialistische applicaties te beleggen.

  Gebruik van berichtuitwisseling maakt het mogelijk om robuuste en betrouwbare notificatieoplossingen te maken.

---

**We gebruiken bij voorkeur berichtuitwisseling als integratiestijl voor notificeren.**

---

Binnen de context van 'berichtuitwisseling' als integratiestijl verstaan we onder een bericht 'een gegevensrecord dat een berichtensysteem kan verzenden via een berichtkanaal'[^1]. Uit te wisselen informatie wordt in de vorm van 1 of meer berichten van zender naar ontvanger worden getransporteerd. Berichten bestaan meestal uit een 'header' met informatie voor bijv. routering van een bericht en een 'payload' die de inhoudelijke gegevens bevat.

Bij berichtuitwisseling is onderscheid te maken in verschillende type berichten:

1. Command message: een bericht dat een opdracht bevat voor de ontvangende applicatie (bijv. om een applicatiefunctie uit te voeren).
2. Document message: een bericht dat gestructureerde gegevens zonder opdracht bevat, bedoeld om informatie over te brengen (bijv. statusinformatie over een plaatsgevonden verwerking)
3. Event message: een bericht waarin gegevens over een plaatsgevonden gebeurtenis zijn opgenomen. In tegenstelling tot een document-message waarbij informatieoverdracht centraal staat is de primaire functie alleen om ontvangende applicaties te laten weten dát er een bepaalde gebeurtenis is opgetreden.

Berichten kunnen van 1 type zijn of een combinatie daarvan. Zo kan een bericht bijv. gegevens bevatten dat de prijs van een product is verhoogd (event-message), maar ook artikelgegevens, inclusief de nieuwe prijs, bevatten (document-message).

We focussen ons op situaties waarin de verzendende applicatie geen eisen of verwachtingen heeft m.b.t wat ontvangers met een bericht doen. Dit betekent dat 'command messages' buiten scope zijn en document- en eventmessages ook geen 'verborgen opdrachten' voor afnemers mogen bevatten.
@@scope

---

**Bij notificeren gebruiken we document- of event-nessages waarin geen opdrachten voor afnemers zijn opgenomen.**

---

In lijn met het uitgangspunt om in situaties waarin vertrouwelijke gegevens zijn betrokken informatie-arm te notificeren is het wenselijk om gebruik te maken van event-messages. Hiermee zijn beveiliging en privacy beter waarborgen via dataminimalisatie en eenmalige passende authenticatie- en autorisatie voorafgaand aan opvraging uit bronregisters.

---

**We maken gebruik van event-messages als bij notificatie vertrouwelijke gegevens zijn betrokken.**

---

Een andere reden om voor event-messages te kiezen is als afnemers grote hoeveelheden gegevens willen ontvangen na het optreden van bepaalde gebeurtenissen. In die situaties kunnen zij via event-messages op de hoogte worden gebracht van het optreden van een gebeurtenis en kunnen zij naar aanleiding daarvan alle gewenste gegevens gaan ophalen bij de aanbieder. Deze manier van stuurgegevens over een kanaal sturen en de inhoudelijke gegevens via een ander kanaal wordt ook wel aangeduid als 'out-of-band control'. Hiermee wordt voorkomen dat voorzieningen die met name bedoeld zijn voor het verwerken van berichten van beperkte omvang overbelast raken.

---

**We maken gebruik van event-messages als bij notificatie grote hoeveelheden gegevens zijn betrokken.**

---

@@@ Geen passief agressieve events in scope waarbij publisher verwacht dat de de ontvanger een bepaalde actie uitvoert.

_ Bij robuuste en betrouwbare oplossingen voor notificeren zijn maatregelen getroffen om goed om te gaan met optredende uitzonderingen en fouten. Bijv. het niet kunnen afleveren van berichten omdat een ontvanger tijdelijk niet bereikbaar is. In zijn algemeenheid geldt dat bij berichtuitwisseling berichten moeten kunnen worden bewaard zolang dat nodig is. Het bekendste mechanisme hiervoor is "queue'ing". Daarbij worden berichten in een rij bewaart en worden ze er op een later moment volgens afspraken uitgehaald. _

# Patronen

Patronen beschrijven zowel het "waarom" als het "hoe" van een voorgestelde oplossing. Ze lichten toe wanneer een oplossing geschikt is en wat de beperkingen zijn. Het zijn echter geen universele kopieer-en-plak-oplossingen: ze hebben allemaal sterke en zwakke punten. Het is essentieel om het juiste patroon voor het juiste doel te gebruiken zodat ontwikkelde oplossingen de vaak tegengestelde krachten en beperkingen in evenwicht te houden.

Er worden verschillende soorten patronen en stijlen onderscheiden die allemaal hun bijdrage kunnen leveren bij het komen tot goede oplossingen voor notificeren:

- Conversatie patronen: hoe kunnen gedistribueerde componenten via 2-weg verkeer met elkaar 'converseren'.
- Integratie stijlen: hoe kan informatieuitwisseling tussen gedistribueerde softwarecomponenten worden gerealiseerd.
- Softwareontwerp patronen: hoe kunnen softwarecomponenten gezamenljik worden ingezet
- Notificatie stijlen en patronen: welke hoofdpatronen zijn te benoemen die specifiek voor notificeren bruikbaar zijn.

## Integratie patronen

Integratiepatronen zijn bruikbaar om flexibele oplossingen op basis van berichtuitwisseling te ontwerpen en realiseren.
Router, Translator, Adapter
=> EIPatterns

## Conversatie / interactie patronen

Partial Processing - In partial processing, the client sends a request to a service and receives an immediate response, but processing continues on the service side. For example, the client sends a request to purchase a vacation package, and the service sends an immediate reply confirming the purchase, then continues on to book the hotel, the flight, the rental car, and so on.

Notificeren is te beschouwen als een 'conversatie' waarbij meerdere deelnemers zijn betrokken. In het geval van notifceren zijn er afspraken dat 1 deelnemer de rol van 'dienstenaanbieder' heeft en 1 of meer andere deelnemers de rol van 'dienstenafnemer' hebben. De dienst is het beschikbaar stellen van gegevens over plaatsgevonden gebeurtenissen binnen het domein van de dienstenaanbieder.
Conversaties kunnen verschillende kenmerken hebben:

- het aantal deelnemers kan constant zijn of kan variëren naarmate het gesprek vordert.
- de aanbieder of afnemers kan initiator van de conversatie zijn
- een bericht binnen de conversatie kan aan één of meer afnemers zijn gericht (maar heeft één afzender)
- een gesprek kan uit één bericht bestaan, maar meestal kent een gesprek meerdere samenhangende berichten.

Een vraag-antwoordgesprek is een voorbeeld waarbij een gesprek een zekere symmetrie kent: zowel de vrager als degene die het antwoord verstrekt verzenden en ontvangen berichten.
In het geval van notificeren is er (meestal) sprake van een asymmetrische relatie: de aanbieder maakt bekend welke gebeurtenissen er hebben plaatsgevonden en stelt die beschikbaar voor afname aan afnemers.

Bij het realiseren van oplossingen voor notificeren is het belangrijk goed te beschrijven welke rollen en verantwoordelijkheden er zijn en hoe conversaties er uit moeten gaan zien.

@@@DDD
The primary purpose of the Bounded Context in Domain Driven Design is to provide the context for the modeling of a domain. Each BC hosts a Domain Model that starts with well-defined concepts/terms (vocabulary), their relationships, and their behaviors. Every component or service in the BC should be designed to fit properly into the BC's domain model.

The BC is effectively a unit of contextual and conceptual design, and by extension then, also a sort of unit of implementation (as implementation follows the designs of the domain model). That the BC tends to provide either autonomy or atomicity depending on what you were going for, is more of an natural extension of treating each domain model and its BC as a unit to be implemented, and that within that unit of implementation we expect its sub-components to have tighter couplings than those occurring between BC's.

## Softwareontwikkel patronen

Voor het notificeren van applicaties zijn verschillende patronen beschikbaar. We richten ons hier met name op patronen waarbij gebruik wordt gemaakt van berichtuitwisseling als integratiestijl.
Binnen patronen is onderscheid te maken in basispatronen en meer specialistische patronen die voortbouwen op de basispatronen.
Hieronder wordt steeds eerst een basispatroon beschreven. Dat kan zelfstandig of in combinatie met andere patronen worden gebruikt, ook voor notificatieoplossingen. Daarna worden specialistische patronen beschreven die voortbouwen op het basispatroon.
'Design Patterns: Elements of Reusable Object-Oriented Software'.

### Observer patroon

Functie: Het kunnen notificeren van applicaties waarbij applicaties los gekoppeld zijn.
Synoniem: publish-subscribe

> The Observer pattern describes how to establish these relationships.The key objects in this pattern are subject and observer. A subject may have any number of dependent observers. All observers are notified whenever the subject undergoes a change in state. In response, each observer will query the subjectto synchronize its state with the subject's state[^2].

- Bij voorkeur hoeft het subject alleen plaatsgevonden gebeurtenissen te melden ('publiceren') zonder kennis te hebben van het feit of, en zo ja hoeveel en welke, observers er zijn.
- Naar aanleiding van een notificatie kunnen observers al dan niet meer informatie opvragen bij het subject.
- Het triggeren van notificeren kan zowel door subject als observer worden gedaan ('push vs pull').
- Notificeren verloopt efficienter als observers specificeren voor welke gebeurtenissen ('aspects') zij genotificeerd willen worden.
- Het Observer patroon kan binnen verschillende integratiestijlen worden toegepast (bijv. bij RPC of Messaging). In de meest pure vorm verloopt de communicatie meestal synchroon.
- Let op! In de oorspronkelijke beschrijving van het observer pattern staat beschreven dat notificering van observers niet altijd door het subject zelf hoeft te worden uitgevoerd maar dat dit ook door een apart object in de rol van 'change manager' kan worden gedaan. In de loop der tijd is men die patroonvariant als een zelfstandig patroon gaan zien: het Publisher-Subscriber patroon ('PubSub'). Dit patroon wordt later als zelfstandig patroon beschreven.

![Observer pattern](images/observer.png)

#### Webhooks

Webhooks zijn een implementatie van het observer pattern.

> Webhooks zijn "door de gebruiker gedefinieerde HTTP-callbacks". Ze worden meestal geactiveerd door een gebeurtenis, zoals het pushen van code naar een opslagplaats of het plaatsen van een opmerking op een blog. Als die gebeurtenis plaatsvindt, doet de bronsite een HTTP-verzoek naar de URL die voor de webhook is geconfigureerd. Gebruikers kunnen ze zo configureren dat gebeurtenissen op de ene site gedrag op een andere site oproepen. - Bron: [Wikipedia](https://en.wikipedia.org/wiki/Webhook)

@@@

### Mediator pattern

Functie: Het faciliteren van communicatie tussen objecten via een mediator zodat ze niet direct gekoppeld hoeven te zijn.
Synoniem: broker

> Define an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring toeach other explicitly, and it lets you vary their interaction independently. <small>- Bron: “Design Patterns: Elements of Reusable Object-Oriented Software"</small>

- Voor het uitwisselen van berichten hoeven objecten niet met alle betrokken componenten relaties aan te gaan maar alleen met de mediator component.
- Het mediator patroon bevordert losse koppeling tussen objecten en faciliteert het flexibel kunnen aanpassen van berichtuitwisseling.
- Het mediator patroon leidt tot minder complexiteit door het vervangen van M:N koppelingen door 1:N koppelingen en het centraal organiseren van (gespecialiseerde) functionaliteit voor gegevensuitwisseling.
- Het mediator patroon kan vaker worden toegepast waarbij sprake is van een keten van mediatoren.

#### Publish subscribe pattern

@@@ wip

## Patronen specifiek voor notificeren

Afhankelijk van de context kan notificeren verschillende doelen hebben en passen daarbij verschillende patronen.

### Event-Notification

Een applicatie levert notificaties ('event messages') om andere applicaties te informeren dat er een gebeurtenis binnen het applicatiedomein heeft plaatsgevonden.

- De zendende applicatie heeft geen verwachtingen of kennis van wat ontvangers doen na ontvangst van de notificatie.
- Ontvangers kunnen zelf beslissen of een notificatie aanleiding geeft om actie te ondernemen (bijv. opvragen van detailgegevens of het starten van een proces).
- Wanneer gegevens worden 'opgevraagd bij de bron' is notificatie nodig om te weten dat er mogelijk relevante gebeurtenissen hebben plaatsgevonden in het bron-domein.

### Event-Carried State Transfer

Een applicatie levert notificaties zodat ontvangers lokaal opgeslagen gegevens kunnen bijwerken ('replicatie') en ze bij benodigd gebruik geen gegevens bij de verzender hoeven op te vragen.

- Notificaties kunnen informatie-rijk of informatie-arm zijn.
- Voordelen zijn dat applicaties los gekoppeld zijn en zelfstandig kunnen werken en bijv. performance wordt verbeterd.
- Nadelen zijn dat gegevens op meerdere plaatsen moeten worden opgeslagen en gesynchroniseerd en consistentie niet altijd is gegarandeerd.

In lijn met het uitgangspunt 'Opvragen bij de bron' zien we dit patroon als onwenselijk wanneer ontvangers brongegevens kunnen opvragen en via Event-notification op de hoogte gesteld kunnen worden van relevante wijzigingen in het brondomein.

### Event-sourcing

Een applicatie legt gebeurtenissen vast ('event log') in een door afnemers raadpleegbare registratie ('event source').
Gebruik van dit patroon betekent in tegenstelling tot voorgaande patronen dat de communicatierichting verandert. Informatie over plaatsgevonden gebeurtenissen wordt niet gebracht ('push') maar afnemers gaan die zelf ophalen ('pull').

- Eventueel kan aanvullend het event-notification patroon worden gebruikt om afnemers op de hoogte te stellen dat er bijv. nieuwe gebeurtenissen hebben plaatsgevonden wat tot een combinatie van 'push' en 'pull' leidt.
- Event-sourcing is zowel bruikbaar voor het vastleggen van system- als business-events.
  - System-events beschrijven opgetreden gebeurtenissen op systeemniveau (bijv. plaatsgevonden CRUD-operaties op resources) en kunnen worden 'afgespeeld' om de actuele situatie van gegevens te reconstrueren.
  - Business-events beschrijven gebeurtenissen zoals die bekend zijn in het betreffende business-domein (bijv. een geplaatste bestelling). In lijn met het uitgangspunt '@@@' richten we ons hier met name op gebruik van event-sourcing voor business-events. Iets waarvoor grondige domeinkennis noodzakelijk is.
- In tegenstelling tot klassieke registraties waarin de actuele toestand van gegevens wordt bijgehouden vormen bij event-sourcing de vastgelegde gebeurtenissen de 'single source of thruth'. Actuele informatie kan worden afgeleid door gebeurtenissen in volgorde 'af te spelen' of door een aparte te raadplegen registratie bij te houden waarin nieuwe gebeurtenissen leiden tot het actualiseren van gegevens.
- Event sourcing is te implementeren via synchrone communicatie (bijv. Git) maar in gedistribueerde omgevingen wordt bij voorkeur gebruik gemaakt van asynchrone berichtuitwisseling.
- Om het uitgangspunt 'Bevragen bij de bron' goed te kunnen realiseren moeten bronregisters (ook) gegevens kunnen leveren die golden op een tijdtip in het verleden ('historie'). Event log@@@

# Protocollen

## REST

Messaging en REST (zie afbeelding)
Waarschijnlijk combi.

## AMQP

## Overige (?)

# Inrichting scenarios

[^1]: Bron: Enterprise Integration Patterns, Gregor Hohpe, Bobby Woolf:
[^2]: Bron: “Design Patterns: Elements of Reusable Object-Oriented Software" - The "Gang of Four": Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides
