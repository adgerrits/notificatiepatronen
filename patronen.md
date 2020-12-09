[Home](index.md)

- [Inleiding](#inleiding)
- [Niet-inhoudelijke aspecten](#niet-inhoudelijke-aspecten)
  - [Synchroon en a-synchrone communicatie](#synchroon-en-a-synchrone-communicatie)
  - [Push en pull](#push-en-pull)
  - [Wel of niet gegarandeerde aflevering](#wel-of-niet-gegarandeerde-aflevering)
  - [...](#)
- [Integratie stijlen](#integratie-stijlen)
- [Patronen](#patronen)
  - [Conversatie patronen](#conversatie-patronen)
  - [Softwareontwikkel patronen](#softwareontwikkel-patronen)
    - [Observer patroon](#observer-patroon)
      - [Webhooks](#webhooks)
    - [Mediator pattern](#mediator-pattern)
      - [Publish subscribe pattern](#publish-subscribe-pattern)
- [Patronen specifiek voor notificeren](#patronen-specifiek-voor-notificeren)
  - [Event-Notification](#event-notification)
  - [Event-Carried State Transfer](#event-carried-state-transfer)
  - [Event-sourcing](#event-sourcing)

# Inleiding

Er zijn veel manieren waarop notificeren is te realiseren. Wat de meest geschikte manier is hangt af van de context. Er is daarom niet één manier die het beste is. Net zoals bij andere ontwerpuitdagingen zijn er wel 'patronen' te onderkennen die oplossingen voor vergelijkbare ontwerpuitdagingen beschrijven. Met behulp daarvan kan binnen een bepaalde context tot een oplossing worden gekomen die maximaal gebruik maakt van bestaande kennis en ervaring.
Hierna worden verschillende soorten patronen beschreven. Om binnen een bepaalde context te kunnen bepalen welke patronen, in welke mate, bruikbaar zijn moet worden beoordeeld of ze passen bij wat binnen de context nodig is. Daarbij spelen een aantal niet-inhoudelijke ('non-functional) aspecten een rol. Hierna geven we daarom eerst een korte beschrijving van een aantal van die terugkerende niet-inhoudelijke aspecten.

# Niet-inhoudelijke aspecten

## Synchroon en a-synchrone communicatie

Systemen kunnen voor notificatie gebruik maken van synchrone communicatie. Bijvoorbeeld als het systeem dat notificeert meteen wil weten of het genotificeerde systeem de notificatie heeft ontvangen en/of goed heeft verwerkt. In lijn met het streven naar losse koppeling van systemen verdient a-synchrone communicatie voor notificeren normaal gesproken de voorkeur. Betrokken systemen kunnen daarbij zelf bepalen wanneer zij hun taken uitvoeren zonder rekening te hoeven houden met andere betrokken partijen.

---

**We gebruiken bij voorkeur a-syncrhone communicatie voor notificeren.**

---

## Push en pull

Notificaties kunnen door het notificerende systeem richting afnemende systemen worden gebracht ('push'). Bijv. als afnemende systemen zo snel mogelijk genotificeerd willen worden. Afnemende systemen kunnen ook zelf beschikbaar gestelde notificaties gaan ophalen ('pull'). Bijv. omdat ze zelf het moment van verwerken willen bepalen of omdat er van hele grote notificatieberichten sprake is. Tenslotte kan er ook een combinatie van beide mechanismen worden gebruikt. Bijv. door eerst een klein notificatiebericht te sturen ('push') met de melding dat er een groot notificatiebericht kan worden opgehaald ('pull'). Er is geen duidelijke voorkeur welk(e) (combinatie van) mechanismes toegepast worden voor notificeren.
Opmerking: het eerder geformuleerde uitgangspunt dat het notificerende systeem zo snel mogelijk nadat een gebeurtenis heeft plaatsgevonden de notificatie daarvan beschikbaar stelt is bij toepassing van beide mechanismen van belang.

## Wel of niet gegarandeerde aflevering

Het kan voor zowel het notificerende, maar vooral voor ontvangende systemen van belang zijn, dat notificaties 'gegarandeerd' worden ontvangen. Gezien de context waarbinnen we notificeren geldt dat het vaak nodig zal zijn. Het kan echter zodanig grote (extra) inspanningen en investeringen vergen dat in bepaalde situaties genoegen wordt genomen met niet-gegarandeerde aflevering.
Opmerking: bij begrippen als 'gegarandeerd' en 'niet-gegarandeerd' geldt altijd dat de context bepalend is voor welke mate van onzekerheid acceptabel is.

---

**We gebruiken bij notificeren bij voorkeur met gegarandeerde aflevering van notificaties.**

---

## ...

# Integratie stijlen

Notificeren is een mechanisme om integratie tussen applicaties te realiseren door op een afgesproken manier gegevens met elkaar uit te wisselen. We onderscheiden 4 soorten integratiestijlen die in meer of minder mate geschikt zijn voor realisatie van oplossingen voor notificeren[^1]

1. Gedeelde database ('Shared database'): Applicaties delen een database met gemeenschappelijk te gebruiken gegevens.
2. Bestandsuitwisseling ('File transfer'): Applicaties produceren bestanden met gegevens die door andere applicaties worden ingelezen.
3. Externe applicatiefunctionaliteit aanroep ('Remote Procedure Invocation'): Applicaties roepen elkaar via beschikbare interfaces aan om gegevens uit te wisselen.
4. Berichtuitwisseling ('Messaging'): Applicaties wisselen berichten met gegevens met elkaar uit.

Alle vier de integratiestijlen kunnen gebruikt worden om oplossingen voor notificeren mee te realiseren. Ze zijn echter niet allemaal even geschikt en ook niet in alle situaties bruikbaar. Binnen de context van overheidsbreed notificeren geldt voor de verschillende stijlen:

- 'Gedeelde database' stijl is niet bruikbaar omdat betrokken applicaties daarvoor toegang moeten hebben tot dezelfde database. Iets dat vaak onwenselijk of onmogelijk is, zeker als er verschillende organisaties bij betrokken zijn. We laten deze stijl daarom verder buiten beschouwing.
- 'Bestandsuitwisseling' is bruikbaar als er het niet nodig is om snel na het optreden van gebeurtenissen te notificeren en/of als het gaat de uitwisseling van grote hoeveelheden gegevens. In lijn met het uitgangspunt dat we gegevens zoveel mogelijk bevragen bij bronregistraties en onnodig dupliceren van gegevens willen voorkomen is deze stijl voor de meeste situaties onwenselijk.
- 'Externe applicatiefunctionaliteit aanroep' is bruikbaar om te notificeren maar heeft als groot nadeel dat er ongewenste afhankelijkheid tussen applicaties ontstaat. Zo moet de aangeroepen applicatie beschikbaar zijn op het moment van aanroep door de andere applicatie om notificatie succesvol te laten zijn.
- 'Berichtuitwisseling' is in het algemeen de meest wenselijke stijl voor integratieoplossingen. Ook wanneer het oplossingen voor notificeren betreft. Door gegevens in zelfstandig te verwerken berichten te vatten zijn betrouwbare oplossingen te realiseren waarbij betrokken applicaties los van elkaar kunnen functioneren en specialistische functionaliteit bij verschillende applicaties is te beleggen. Zeker wanneer gebruik wordt gemaakt van asynchrone berichtuitwisseling.

---

**We gebruiken bij voorkeur berichtuitwisseling als integratiestijl voor notificeren.**

---

Bij berichtuitwisseling is onderscheid te maken in verschillende type berichten:

1. Command message: een bericht dat een opdracht bevat voor de ontvangende applicatie (bijv. om een applicatiefunctie uit te voeren).
2. Document message: een bericht dat gestructureerde gegevens zonder opdracht bevat, bedoeld om informatie over te brengen (bijv. statusinformatie over een plaatsgevonden verwerking)
3. Event message: een bericht waarin gegevens over een plaatsgevonden gebeurtenis zijn opgenomen. In tegenstelling tot een document-message waarbij informatieoverdracht centraal staat is de primaire functie om kort na het optreden van een gebeurtenis ontvangende applicaties daarvan in kennis te stellen.

Berichten kunnen van 1 type zijn of een combinatie daarvan. Zo kan een bericht bijv. gegevens bevatten dat de prijs van een product is verhoogd (event-message), maar ook artikelgegevens, inclusief de nieuwe prijs, bevatten (document-message).

In lijn met het uitgangspunt om in situaties waarin vertrouwelijke gegevens zijn betrokken informatie-arm te notificeren is het wenselijk om gebruik te maken van event-messages. Hiermee zijn beveiliging en privacy beter waarborgen via dataminimalisatie en eenmalige passende authenticatie- en autorisatie voorafgaand aan opvraging uit bronregisters.

---

**We maken gebruik van event-messages als bij notificatie vertrouwelijke gegevens zijn betrokken.**

---

Een andere reden om voor event-messages te kiezen is als afnemers grote hoeveelheden gegevens willen ontvangen na het optreden van bepaalde gebeurtenissen. In die situaties kunnen zij via event-messages op de hoogte worden gebracht van het optreden van een gebeurtenis en kunnen zij naar aanleiding daarvan alle gewenste gegevens gaan ophalen bij de aanbieder. Deze manier van stuurgegevens over een kanaal sturen en de inhoudelijke gegevens via een ander kanaal wordt ook wel aangeduid als 'out-of-band control'. Hiermee wordt voorkomen dat voorzieningen die met name bedoeld zijn voor het verwerken van berichten van beperkte omvang overbelast raken.

---

**We maken gebruik van event-messages als bij notificatie grote hoeveelheden gegevens zijn betrokken.**

---

# Patronen

Patronen beschrijven zowel het "waarom" als het "hoe" van een voorgestelde oplossing. Ze lichten toe wanneer een oplossing geschikt is en wat de beperkingen zijn. Het zijn echter geen universele kopieer-en-plak-oplossingen: ze hebben allemaal sterke en zwakke punten. Het is essentieel om het juiste patroon voor het juiste doel te gebruiken zodat ontwikkelde oplossingen de vaak tegengestelde krachten en beperkingen in evenwicht te houden.

Er worden verschillende soorten patronen en stijlen onderscheiden die allemaal hun bijdrage kunnen leveren bij het komen tot goede oplossingen voor notificeren:

- Conversatie patronen: hoe kunnen gedistribueerde componenten via 2-weg verkeer met elkaar 'converseren'.
- Integratie stijlen: hoe kan informatieuitwisseling tussen gedistribueerde softwarecomponenten worden gerealiseerd.
- Softwareontwerp patronen: hoe kunnen softwarecomponenten gezamenljik worden ingezet
- Notificatie stijlen en patronen: welke hoofdpatronen zijn te benoemen die specifiek voor notificeren bruikbaar zijn.

## Conversatie patronen

Notificeren is te beschouwen als een 'conversatie' waarbij meerdere deelnemers zijn betrokken. In het geval van notifceren zijn er afspraken dat 1 deelnemer de rol van 'dienstenaanbieder' heeft en 1 of meer andere deelnemers de rol van 'dienstenafnemer' hebben. De dienst is het beschikbaar stellen van gegevens over plaatsgevonden gebeurtenissen binnen het domein van de dienstenaanbieder.
Conversaties kunnen verschillende kenmerken hebben:

- het aantal deelnemers kan constant zijn of kan variëren naarmate het gesprek vordert.
- de aanbieder of afnemers kan initiator van de conversatie zijn
- een bericht binnen de conversatie kan aan één of meer afnemers zijn gericht (maar heeft één afzender)
- een gesprek kan uit één bericht bestaan, maar meestal kent een gesprek meerdere samenhangende berichten.

Een vraag-antwoordgesprek is een voorbeeld waarbij een gesprek een zekere symmetrie kent: zowel de vrager als degene die het antwoord verstrekt verzenden en ontvangen berichten.
In het geval van notificeren is er (meestal) sprake van een asymmetrische relatie: de aanbieder maakt bekend welke gebeurtenissen er hebben plaatsgevonden en stelt die beschikbaar voor afname aan afnemers.

Bij het realiseren van oplossingen voor notificeren is het belangrijk goed te beschrijven welke rollen en verantwoordelijkheden er zijn en hoe conversaties er uit moeten gaan zien.

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
- Notificering van observers hoeft niet altijd door het subject zelf te worden uitgevoerd. Hiervoor kan een apart object ('change manager') worden ingeschakeld. Deze implementatie van het Observer patroon wordt vaak aangeduid als het Publisher-Subscriber ('PubSub') patroon waarbij (asynchrone) communicatie tussen subject en observers verloopt via een tussenliggende component ('broker'). Dit patroon is bruikbaar als losse koppeling tussen subject en observers belangrijk is. Het patroon wordt later apart toegelicht.

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

# Patronen specifiek voor notificeren

Afhankelijk van de context kan notificeren verschillende doelen hebben en passen daarbij verschillende patronen.

## Event-Notification

Een applicatie levert notificaties ('event messages') om andere applicaties te informeren dat er een gebeurtenis binnen het applicatiedomein heeft plaatsgevonden.

- De zendende applicatie heeft geen verwachtingen of kennis van wat ontvangers doen na ontvangst van de notificatie.
- Ontvangers kunnen zelf beslissen of een notificatie aanleiding geeft om actie te ondernemen (bijv. opvragen van detailgegevens of het starten van een proces).
- Wanneer gegevens worden 'opgevraagd bij de bron' is notificatie nodig om te weten dat er mogelijk relevante gebeurtenissen hebben plaatsgevonden in het bron-domein.

## Event-Carried State Transfer

Een applicatie levert notificaties zodat ontvangers lokaal opgeslagen gegevens kunnen bijwerken ('replicatie') en ze bij benodigd gebruik geen gegevens bij de verzender hoeven op te vragen.

- Notificaties kunnen informatie-rijk of informatie-arm zijn.
- Voordelen zijn dat applicaties los gekoppeld zijn en zelfstandig kunnen werken en bijv. performance wordt verbeterd.
- Nadelen zijn dat gegevens op meerdere plaatsen moeten worden opgeslagen en gesynchroniseerd en consistentie niet altijd is gegarandeerd.

In lijn met het uitgangspunt 'Opvragen bij de bron' zien we dit patroon als onwenselijk wanneer ontvangers brongegevens kunnen opvragen en via Event-notification op de hoogte gesteld kunnen worden van relevante wijzigingen in het brondomein.

## Event-sourcing

Een applicatie legt gebeurtenissen vast ('event log') in een door afnemers raadpleegbare registratie ('event source').
Gebruik van dit patroon betekent in tegenstelling tot voorgaande patronen dat de communicatierichting verandert. Informatie over plaatsgevonden gebeurtenissen wordt niet gebracht ('push') maar afnemers gaan die zelf ophalen ('pull').

- Eventueel kan aanvullend het event-notification patroon worden gebruikt om afnemers op de hoogte te stellen dat er bijv. nieuwe gebeurtenissen hebben plaatsgevonden wat tot een combinatie van 'push' en 'pull' leidt.
- Event-sourcing is zowel bruikbaar voor het vastleggen van system- als business-events.
  - System-events beschrijven opgetreden gebeurtenissen op systeemniveau (bijv. plaatsgevonden CRUD-operaties op resources) en kunnen worden 'afgespeeld' om de actuele situatie van gegevens te reconstrueren.
  - Business-events beschrijven gebeurtenissen zoals die bekend zijn in het betreffende business-domein (bijv. een geplaatste bestelling). In lijn met het uitgangspunt '@@@' richten we ons hier met name op gebruik van event-sourcing voor business-events. Iets waarvoor grondige domeinkennis noodzakelijk is.
- In tegenstelling tot klassieke registraties waarin de actuele toestand van gegevens wordt bijgehouden vormen bij event-sourcing de vastgelegde gebeurtenissen de 'single source of thruth'. Actuele informatie kan worden afgeleid door gebeurtenissen in volgorde 'af te spelen' of door een aparte te raadplegen registratie bij te houden waarin nieuwe gebeurtenissen leiden tot het actualiseren van gegevens.
- Event sourcing is te implementeren via synchrone communicatie (bijv. Git) maar in gedistribueerde omgevingen wordt bij voorkeur gebruik gemaakt van asynchrone berichtuitwisseling.
- Om het uitgangspunt 'Bevragen bij de bron' goed te kunnen realiseren moeten bronregisters (ook) gegevens kunnen leveren die golden op een tijdtip in het verleden ('historie'). Event log@@@

[^1]: Bron: Enterprise Integration Patterns, Gregor Hohpe, Bobby Woolf:
[^2]: Bron: “Design Patterns: Elements of Reusable Object-Oriented Software" - The "Gang of Four": Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides
