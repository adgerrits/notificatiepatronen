


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

Hierna worden patronen die bruikbaar zijn bij het ontwerpen van oplossingen voor het notificeren van systemen. 

# Integratie-stijlen
Notificeren is een mechanisme om integratie tussen applicaties te realiseren door op een afgesproken manier gegevens met elkaar uit te wisselen.
We onderscheiden 4 soorten integratiestijlen:
1. Gedeelde database ('Shared database'): Applicaties delen een database met gemeenschappelijk te gebruiken gegevens.
2. Bestandsuitwisseling ('File transfer'): Applicaties produceren bestanden met gegevens die door andere applicaties worden ingelezen.
3. Externe applicatiefunctionaliteit aanroep ('Remote Procedure Invocation'): Applicaties roepen elkaar via beschikbare interfaces aan om gegevens uit te wisselen.
4. Berichtuitwisseling ('Messaging'): Applicaties wisselen berichten met gegevens met elkaar uit.

Alle vier de integratiestijlen kunnen bruikbaar zijn om oplossingen voor notificeren mee te realiseren. Ze zijn echter niet allemaal even geschikt en ook niet altijd bruikbaar. Binnen de context van overheidsbreed notificeren geldt voor de verschillende stijlen dat:
- 'Gedeelde database' stijl is niet bruikbaar omdat betrokken applicaties daarbij toegang moeten hebben tot dezelfde database. Iets dat vaak onwenselijk of onmogelijk is. We laten deze stijl daarom hier verder buiten beschouwing.
- 'Bestandsuitwisseling' is bruikbaar wanneer als er geen noodzaak is om snel na het optreden van gebeurtenissen te notificeren en/of als het gaat om heel grote hoeveelheden gegevens. In lijn met het uitgangspunt dat we gegevens zoveel mogelijk bevragen bij bronregistraties en onnodig dupliceren van gegevens willen voorkomen zien we deze stijl in de meeste situaties als onwenselijk.
- 'Externe applicatiefunctionaliteit aanroep' is bruikbaar voor notificeren maar heeft in veel situaties als belangrijk nadeel dat er ongewenste afhankelijkheid tussen applicaties ontstaat. Bijv. omdat een aangeroepen applicatie beschikbaar moet zijn op het moment van aanroep om gegevensuitwisseling te kunnen laten slagen. 
- 'Berichtuitwisseling' is in het algemeen de meest wenselijke integratiestijl, ook voor oplossingen voor notificeren. Door gegevens in zelfstandig te verwerken berichten te vatten zijn betrouwbare oplossingen te realiseren waarbij betrokken applicaties los van elkaar kunnen functioneren en specialistische functionaliteit bij verschillende applicaties is te beleggen. Zeker wanneer gebruik wordt gemaakt van asynchroon berichtenverkeer dat losse koppeling tussen applicaties verhoogt.













# Notificatie patronen
