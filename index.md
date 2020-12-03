- [Terminologie en toelichting](#terminologie-en-toelichting)
- [Patronen](#patronen)
- [Conversatie](#conversatie)
- [Integratie-stijlen](#integratie-stijlen)
  - [Berichtuitwisseling en type berichten](#berichtuitwisseling-en-type-berichten)
- [Basis-patronen, ook bruikbaar voor notificeren](#basis-patronen-ook-bruikbaar-voor-notificeren)
    - [Observer patroon](#observer-patroon)
    - [Mediator pattern](#mediator-pattern)
- [Patronen voor notificeren](#patronen-voor-notificeren)
  - [Event-Notification](#event-notification)
  - [Event-Carried State Transfer](#event-carried-state-transfer)
  - [Event-sourcing](#event-sourcing)

# Terminologie en toelichting

Patroon
: Een ontwerppatroon is de herbruikbare vorm van een oplossing voor een ontwerpprobleem.

> Elk patroon beschrijft een probleem dat zich keer op keer voordoet in onze omgeving, en beschrijft vervolgens de kern van de oplossing voor dat probleem, zodanig dat je deze oplossing een miljoen keer kunt gebruiken, zonder het ooit twee keer op dezelfde manier te doen. - Christopher Alexander

Patronen-taal
: Een georganiseerde verzameling ontwerppatronen die betrekking hebben op een bepaald onderwerp, wordt een patroontaal genoemd. Deze taal geeft een gemeenschappelijke terminologie voor het bespreken van de situaties waarmee ontwerpers worden geconfronteerd.

Gebeurtenis-gedreven architectuur
: Een softwarearchitectuurparadigma dat de productie, detectie, consumptie van en reactie op gebeurtenissen bevordert.

Notificeren
: Het op de hoogte brengen van een partij van een plaatsgevonden gebeurtenis.

# Patronen

Patronen beschrijven zowel het "waarom" als het "hoe" van een voorgestelde oplossing. Ze lichten toe wanneer een oplossing geschikt is en wat de beperkingen zijn. Het zijn echter geen universele kopieer-en-plak-oplossingen: ze hebben allemaal sterke en zwakke punten. Het is essentieel om het juiste patroon voor het juiste doel te gebruiken zodat ontwikkelde oplossingen de vaak tegengestelde krachten en beperkingen in evenwicht te houden.

Er zijn verschillende soorten patronen te onderscheiden:

- Conversatie patronen: patronen die beschrijven hoe los gekoppelde gedistribueerde services samen kunnen werken; met duidelijkheid in rollen en verantwoordelijkheden van iedere deelnemer aan de conversatie.
- Ontwerp patronen: patronen die voor onderdelen van een conversatie beschrijven hoe benodigde functionaliteit is te ontwwerpen en realiseren.

# Conversatie

Een conversatie is een uitwisseling van individuele, maar gerelateerde berichten in de loop van de tijd. EEN gesprek bestaat uit de volgende elementen:

- Het gesprek vindt plaats tussen twee of meer deelnemers. Het aantal deelnemers kunnen constant zijn of kunnen variëren naarmate het gesprek vordert. Een deelnemer treedt doorgaans op als initiator, d.w.z. het begint het gesprek door het eerste bericht te verzenden.
- Deelnemers communiceren door berichten uit te wisselen. Elk bericht heeft één afzender en een of meer ontvangers.
- Berichten kunnen worden geassocieerd of gecorreleerd aan het gesprek. Dit is meestal bereikt door het opnemen van een conversatie-ID of correlatie-ID in elk
  bericht.

Een vraag-antwoordgesprek kent twee deelnemersrollen: serviceprovider en een serviceconsument. Zowel de provider als de consument verzenden en ontvangen berichten waardoor het gesprek een zekere symmetrie krijgt. In het geval van notificeren is er sprake van een asymmetrische relatie: de provider maakt bekend welke gebeurtenissen binnen zijn domein hebben plaatsgevonden en stelt die beschikbaar voor afname de consument(en).

# Integratie-stijlen

Notificeren is een mechanisme om integratie tussen applicaties te realiseren door op een afgesproken manier gegevens met elkaar uit te wisselen.
We onderscheiden 4 soorten integratiestijlen:

1. Gedeelde database ('Shared database'): Applicaties delen een database met gemeenschappelijk te gebruiken gegevens.
2. Bestandsuitwisseling ('File transfer'): Applicaties produceren bestanden met gegevens die door andere applicaties worden ingelezen.
3. Externe applicatiefunctionaliteit aanroep ('Remote Procedure Invocation'): Applicaties roepen elkaar via beschikbare interfaces aan om gegevens uit te wisselen.
4. Berichtuitwisseling ('Messaging'): Applicaties wisselen berichten met gegevens met elkaar uit.

Alle vier de integratiestijlen kunnen gebruikt worden om oplossingen voor notificeren mee te realiseren. Ze zijn echter niet allemaal even geschikt en ook niet in alle situaties bruikbaar. Binnen de context van overheidsbreed notificeren geldt voor de verschillende stijlen:

- 'Gedeelde database' stijl is niet bruikbaar omdat betrokken applicaties daarvoor toegang moeten hebben tot dezelfde database. Iets dat vaak onwenselijk of onmogelijk is, zeker als er verschillende organisaties bij betrokken zijn. We laten deze stijl daarom verder buiten beschouwing.
- 'Bestandsuitwisseling' is bruikbaar als er het niet nodig is om snel na het optreden van gebeurtenissen te notificeren en/of als het gaat de uitwisseling van grote hoeveelheden gegevens. In lijn met het uitgangspunt dat we gegevens zoveel mogelijk bevragen bij bronregistraties en onnodig dupliceren van gegevens willen voorkomen is deze stijl voor de meeste situaties onwenselijk.
- 'Externe applicatiefunctionaliteit aanroep' is bruikbaar om te notificeren maar heeft als groot nadeel dat er ongewenste afhankelijkheid tussen applicaties ontstaat. Zo moet de aangeroepen applicatie beschikbaar zijn op het moment van aanroep door de andere applicatie om notificatie succesvol te laten zijn.
- 'Berichtuitwisseling' is in het algemeen de meest wenselijke stijl voor integratieoplossingen. Ook wanneer het oplossingen voor notificeren betreft. Door gegevens in zelfstandig te verwerken berichten te vatten zijn betrouwbare oplossingen te realiseren waarbij betrokken applicaties los van elkaar kunnen functioneren en specialistische functionaliteit bij verschillende applicaties is te beleggen. Zeker wanneer gebruik wordt gemaakt van asynchrone berichtuitwisseling.

## Berichtuitwisseling en type berichten

Bij berichtuitwisseling is onderscheid te maken in verschillende type berichten:

1. Command message: een bericht dat een opdracht bevat voor de ontvangende applicatie (bijv. om een applicatiefunctie uit te voeren).
2. Document message: een bericht dat gestructureerde gegevens zonder opdracht bevat, bedoeld om informatie over te brengen (bijv. statusinformatie over een plaatsgevonden verwerking)
3. Event message: een bericht waarin gegevens over een plaatsgevonden gebeurtenis zijn opgenomen. In tegenstelling tot een document-message waarbij informatieoverdracht centraal staat is de primaire functie om kort na het optreden van een gebeurtenis ontvangende applicaties daarvan in kennis te stellen.

Berichten kunnen van 1 type zijn of een combinatie daarvan. Zo kan een bericht bijv. gegevens bevatten dat de prijs van een product is verhoogd (event-message), maar ook artikelgegevens, inclusief de nieuwe prijs, bevatten (document-message).

In lijn met het uitgangspunt om in situaties waarin vertrouwelijke gegevens zijn betrokken informatie-arm te notificeren is het wenselijk om gebruik te maken van event-messages. Hiermee gerealiseerde data-minimalisatie en uitvoering van authenticatie- en autorisatiecontroles bij opvraging uit bronregisters leidt tot beterer beveiliging en privacywaarborgen.

# Basis-patronen, ook bruikbaar voor notificeren

Voor het notificeren van applicaties zijn verschillende patronen beschikbaar. We richten ons hier met name op patronen waarbij gebruik wordt gemaakt van berichtuitwisseling als integratiestijl.
Binnen patronen is onderscheid te maken in basispatronen en uitgewerkte patronen die voortbouwen op basispatronen. Hieronder worden eerste enkele basispatronen beschreven die zelfstandig of in combinatie met andere patronen zijn te gebruiken voor oplossingen om te notificeren.
De beschrijving van basispatronen is grotendeels gebaseerd op het boek 'Design Patterns: Elements of Reusable Object-Oriented Software'.

### Observer patroon

Functie: Het kunnen notificeren van applicaties waarbij applicaties los gekoppeld zijn.
Synoniem: publish-subscribe

> The Observer pattern describes how to establish these relationships.The key objects in this pattern are subject and observer. A subject may have any number of dependent observers. All observers are notified whenever the subject undergoes a change in state. In response, each observer will query the subjectto synchronize its state with the subject's state.

- Bij voorkeur hoeft het subject alleen plaatsgevonden gebeurtenissen te melden ('publiceren') zonder kennis te hebben van het feit of, en zo ja hoeveel en welke, observers er zijn.
- Naar aanleiding van een notificatie kunnen observers al dan niet meer informatie opvragen bij het subject.
- Het triggeren van notificeren kan zowel door subject als observer worden gedaan ('push vs pull').
- Notificeren verloopt efficienter als observers specificeren voor welke gebeurtenissen ('aspects') zij genotificeerd willen worden.
- Het Observer patroon wordt veel toegepast bij softwareontwikkeling waar onderdelen elkaar (vaak synchroon) notificeren. Bijv. bij gebruik van het Model-View-Controller model. Wanneer sprake is van componenten in gescheiden omgevingen kan daarvoor bijv. de RPC-integratiestijl worden toegepast.
- Notificering van observers hoeft niet altijd door het subject zelf te worden uitgevoerd. Hiervoor kan een apart object ('change manager') worden ingeschakeld. Deze implementatie van het Observer patroon duidt men vaak aan als het Publisher-Subscriber ('PubSub') patroon waarbij (asynchrone) communicatie tussen subject en observers verloopt via een tussenliggende component ('broker'). Dit patroon is bijv. bruikbaar voor notificeren wanneer subject en observers los gekoppeld moeten zijn.

![Observer pattern](images/observer.png)

### Mediator pattern

Functie: Het faciliteren van communicatie tussen objecten via een mediator zodat ze niet direct gekoppeld hoeven te zijn.
Synoniem: broker

> Define an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring toeach other explicitly, and it lets you vary their interaction independently.

- Voor het uitwisselen van berichten hoeven objecten niet met alle betrokken componenten relaties aan te gaan maar alleen met de mediator component.
- Het mediator patroon bevordert losse koppeling tussen objecten en faciliteert het flexibel kunnen aanpassen van berichtuitwisseling.
- Het mediator patroon leidt tot minder complexiteit door het vervangen van M:N koppelingen door 1:N koppelingen en het centraal organiseren van (gespecialiseerde) functionaliteit voor gegevensuitwisseling.
- Het mediator patroon kan vaker worden toegepast waarbij sprake is van een keten van mediatoren.

# Patronen voor notificeren

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
