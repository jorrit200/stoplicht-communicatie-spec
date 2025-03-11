# stoplicht-communicatie-spec
Dit document beschrijft de communicatie tussen de kruispuntsimulatie en de controller van het stoplichtprobleem.

## Onderdelen
Het product van de opdracht bestaat uit 3 verschillende componenten. 
Elk component is losjes aan de andere gekoppeld, 
en de koppeling bestaat alleen uit communicatie zoals beschreven in deze standaard.
Elk van de 3 onderdelen wordt apart geïmplementeerd door elke groepje. 
Elk onderdeel is uitwisselbaar met een implementatie van een ander groepje.

De Onderdelen zijn:
### Controller
De *controller* is verantwoordelijk voor de staat van de stoplichten. 
Dit onderdeel probeert een optimale verkeer-flow te behalen, en ongelukken te voorkomen 
(het liefst in omgekeerde volgorde qua prioriteit).
Dit onderdeel leest de staat van alle sensoren, die gebruikt *kunnen* worden om realtime-besluiten te maken.

### Simulatie
De *simulatie* is verantwoordelijk voor het nabootsen van een reële verkeerssituatie.
De simulatie bepaald het gedrag van het verkeer, beide van individuelen voertuigen, en de hoeveelheid.
De simulatie weergeeft het verkeer en de staat van de stoplichten etc.
De simulatie is verantwoordelijk voor de staat van de sensoren (stoplicht sensoren, en speciale sensoren).

### Regressie tester
De *regressie tester* luistert naar alle communicatie tussen de [*controller*](#controller) en de [*simulatie*](#simulatie),
en geeft een waarschuwing als de communicatie niet volgens het protocol gaat 
(verkeerd JSON-formaat, topic wordt niet gepublished, topic bevat extra informatie, topic mist informatie, waarde in topic is niet mogelijk).
De regressie tester heeft absoluut geen invloed op het gedrag van de andere componenten.

## Communicatie
De communicatie tussen de [onderdelen](#onderdelen) gaat via [ZeroMQ](./zeromq/README.md),
met gebruik van het [PUB/SUB-patroon](./zeromq/README.md#publish---subscribe).
De communicatie gaat via een aantal topics. 
Elk topic heeft één publisher, de [controller](#controller) of de [simulatie](#simulatie), 
en de andere twee [onderdelen](#onderdelen) subscriben op deze topic. 
De [regressie tester](#regressie-tester) subscribed dus op alle topics.
De topics staat beschreven in [topics](./topics/README.md), 
waar voor elke topic een specificatie en voorbeeld beschikbaar zijn.
 
## Problemen met deze oplossing?
- Als je een verandering wilt zien in wat de afspraken zijn, of daar een vraag over hebt, maak een [issue](https://github.com/jorrit200/stoplicht-communicatie-spec/issues) aan.
- Als je denkt dat deze spec afwijkt van een gemaakte afspraak, of dat een gemaakte afspraak nog niet geïmplementeerd is, maak een [pull-request](https://github.com/jorrit200/stoplicht-communicatie-spec/pulls) aan.
- communiceer het naar de anderen in discord of de lessen.
- veranderingen worden democratisch behandeld. Finale besluitvorming gebeurt altijd in een vergadering

Het huidige communicatie protocol mag gezien worden als finaal, 
en kan alleen aangepast worden als blijkt dat het protocol in strijd staat met gemaakte afspraken, 
of het de implementatie van één of meer [onderdelen](#onderdelen) bijzonder (onnodig) moeilijk maakt (geldt niet voor specifiek implementaties).