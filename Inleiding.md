- [Federatief georganiseerd applicatielandschap](#federatief-georganiseerd-applicatielandschap)
- [Gedistribueerde communicatie](#gedistribueerde-communicatie)
- [Losse koppeling](#losse-koppeling)
- [Grote samenhang](#grote-samenhang)

# Federatief georganiseerd applicatielandschap

Bij notificeren zijn minimaal twee systemen betrokken: een systeem met de rol van ‘aanbieder van notificaties’ en systeem met de rol van ‘afnemer van notificaties’. Er kunnen ook 1 of meer systemen in een ondersteunende rol betrokken zijn. De Nederlandse overheid kent ruim 1800 organisaties waar talloze systemen gebruikt worden. Er is sprake van een federatief georganiseerd applicatielandschap waarin systemen vanuit verschillende omgevingen met elkaar moeten kunnen communiceren. Met ‘federatief’ bedoelen we dat systemen autonoom zijn en er een minimaal aantal afspraken en standaarden zijn om gezamenlijk te kunnen opereren op gebieden waar dat nodig is. Bijvoorbeeld bij notificeren omdat zo veel mogelijk systemen, in de rol van aanbieder of afnemer, deel uit moeten kunnen maken van notificatieprocessen.

# Gedistribueerde communicatie

Het feit dat bij notificatie betrokken systemen zich in verschillende omgevingen bevinden brengt, net als bij andere vormen van gedistribueerde communicatie, een aantal onzekerheden met zich mee. Bijvoorbeeld als benodigde systemen niet actief zijn of te traag zijn, als verstuurde berichten kwijt of corrupt raken of als niet duidelijk is of een systeem ontvangen notificaties al heeft ontvangen of verwerkt. Voldoende robuust inrichten van overheidsbreed notificeren vereist daarom duidelijke afspraken en, afhankelijk van de context, speciale maatregelen.

In lijn met de servicegerichte architectuur van de NORA geldt (ook) voor notificeren het uitgangspunt dat systemen onafhankelijk van elkaar moeten kunnen functioneren en voldoende interoperabel moeten zijn om goed samen te werken. Een belangrijk ontwerpprincipe daarbij is 'losse koppeling, grote samenhang; (‘low coupling, high cohesion’). Systemen moeten los van elkaar kunnen functioneren en alleen bij elkaar horende functionaliteit bevatten.

# Losse koppeling

De gewenste ‘losse koppeling’ kan op verschillende aspecten betrekking hebben:

- Tijd – Om te notificeren hoeven niet alle betrokken systemen gelijktijdig actief te zijn. Dit vereist een vorm van ‘bufferen’ waarbij de aanbieder geen onmiddellijk antwoord nodig heeft. Bijv. via toepassing van asynchrone communicatie of gebruik van queue’s.
- Plaats – Betrokken systemen moeten verplaatst kunnen worden. Dit vereist duidelijke afspraken bij wijzigingen of, nog liever, het automatisch kunnen opzoeken van locaties waar systemen bereikbaar zijn.
- Versie – Notificeren moet mogelijk blijven als het aanbiedende systeem uitbreidingen of wijzigingen aanbrengt. Afnemende systemen houden zich aan alle eisen die een versie kent.
- Kardinaliteit – Aanbiedende systemen hoeven geen rekening te houden met het aantal notificaties afnemende systemen. Bijvoorbeeld door het beschikbaar stellen aan afnemers uit te besteden aan een ondersteunend systeem.
- Vindbaarheid – Aanroep van services vindt plaats via logische namen en adressen die vindbaar zijn in catalogi.
- Technologie – Betrokken systemen kunnen van verschillende technologieën gebruik maken. Bijv. door een aantal gemeenschappelijk bruikbare standaarden af te spreken.
- Protocol en formaat – Notificeren moet mogelijk zijn bij systemen die andere protocollen en (bericht)formaten gebruiken.
- Interface – Interfaces zijn gestandaardiseerd zodat nieuwe systemen eenvoudig ontwikkeld, vervangen of toegevoegd kunnen worden.
  Welke aspecten het meeste van belang zijn is contextafhankelijk. Voor alle aspecten geldt echter dat er altijd afspraken en standaarden nodig zijn.

# Grote samenhang

Net zoals voor ‘losse koppeling’ geldt ook voor 'grote samenhang' dat er verschillende aspecten zijn:

- Domein – Het moet helder zijn welke inhoudelijke domeinen en scheidslijnen we onderkennen om notificeren in lijn daarmee te organiseren. Bijv. om duidelijkheid te kunnen verschaffen over de betekenis van gebeurtenissen en notificatiegegevens.
- Organisatie – Organisaties voeren met elkaar samenhangende taken uit die aan hen zijn toebedeeld en voeren geen taken uit die niet bij hen thuis horen. Bijv. door niet-primaire taken door een andere organisatie te laten uitvoeren.
- Systemen – Systemen beperken zich tot helder afgebakende samenhangende functionaliteit. Bijv. door wat geen kernfunctionaliteit is door een ander systeem te laten uitvoeren.
  Ook hier geldt dat de context bepaalt welke aspecten, in welke mate, van belang zijn.
