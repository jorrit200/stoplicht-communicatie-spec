# Stoplicht Communicatie Specificatie

Dit document beschrijft de communicatie tussen de kruispuntsimulatie en de controller van het stoplichtprobleem.

## Inleiding

- [Systeem Overzicht](#systeem-overzicht)
  - [Controller](#controller)
  - [Simulatie](#simulatie)
  - [Regressie Tester](#regressie-tester)
- [Communicatie](#communicatie)
  - [Topics](#topics)
- [Belangrijke Concepten](#belangrijke-concepten)
  - [Stoplichten](#stoplichten)
  - [Sensoren](#sensoren)
- [Problemen met deze oplossing?](#problemen-met-deze-oplossing)

## Systeem Overzicht

Het product bestaat uit drie los gekoppelde componenten. Elk component wordt apart geïmplementeerd en is uitwisselbaar met implementaties van andere groepen.

### Controller

De controller is verantwoordelijk voor de staat van de stoplichten. Dit onderdeel probeert een optimale verkeersdoorstroming te behalen en ongelukken te voorkomen (in die volgorde qua prioriteit). De controller leest de staat van alle sensoren voor realtime-besluitvorming.

### Simulatie

De simulatie is verantwoordelijk voor het nabootsen van een reële verkeerssituatie. Het bepaalt het gedrag van individuele voertuigen en de verkeersdichtheid. De simulatie toont het verkeer en de staat van de stoplichten, en beheert de sensoren (stoplicht sensoren en speciale sensoren).

### Regressie Tester

De regressie tester valideert de communicatie tussen de controller en simulatie. Het geeft waarschuwingen bij protocol schendingen (verkeerd JSON-formaat, ontbrekende topics, ongeldige waarden). De tester heeft geen invloed op het systeemgedrag.

## Communicatie

De componenten communiceren via ZeroMQ met het PUB/SUB-patroon. Elke topic heeft één publisher (controller of simulatie) en twee subscribers. De regressie tester luistert naar alle topics.

### Topics

De communicatie verloopt via verschillende topics:

- Stoplichten: Status updates van verkeerslichten
- Sensoren:
  - Rijbaan sensoren: Voertuigdetectie per rijbaan
  - Brug sensoren: Monitoring van bruggebied
  - Speciale sensoren: Specifieke gebiedsmonitoring
- Tijd: Synchronisatie en timing informatie
- Voorrangsvoertuig: Prioriteitsverkeer meldingen

Zie [Communicatie Protocol](./zeromq/README.md) voor implementatiedetails en [Topics](./topics/README.md) voor berichtformaten.

## Belangrijke Concepten

### Stoplichten

- Identificatie: `"groep.baan"` formaat
- Staten: `"groen"|"oranje"|"rood"`
- Locaties: Zie [kruispunt_verkeerslichten.png](./assets/Kruispunt_verkeerslichten.png) en [brug_verkeerslichten.png](./assets/Brug_verkeerslichten.png)
- Sensoren: Twee per stoplicht (voor en 35m achter de stopstreep)
- Stoplichten 61-64 zijn hefbomen voor de brug
- Configuratie: Zie [intersection data](./intersectionData/README.md) voor gedetailleerde informatie over rijbanen en stoplichten

### Sensoren

- Stoplicht sensoren: Detecteren voertuigen bij stoplichten
- Speciale sensoren: Monitoren specifieke gebieden (zie [brug_verkeerslichten_sensoren.svg](./assets/Brug_verkeerslichten_sensoren.svg))
  - Voorkomen dat hefbomen sluiten met voertuigen op de brug
  - Regelen verkeer naar de Damenlaan bij brugfiles
- Sensor configuratie: Gedetailleerde informatie beschikbaar in [intersection data](./intersectionData/README.md)

# Problemen met deze oplossing?

- Als je een verandering wilt zien in wat de afspraken zijn, of daar een vraag over hebt, maak een [issue](https://github.com/jorrit200/stoplicht-communicatie-spec/issues) aan
- Als je denkt dat deze spec afwijkt van een gemaakte afspraak, of dat een gemaakte afspraak nog niet geïmplementeerd is, maak een [pull-request](https://github.com/jorrit200/stoplicht-communicatie-spec/pulls) aan
- Communiceer het naar de anderen in Discord of de lessen
- Veranderingen worden democratisch behandeld. Finale besluitvorming gebeurt altijd in een vergadering

Het huidige communicatie protocol mag gezien worden als finaal, en kan alleen aangepast worden als blijkt dat het protocol in strijd staat met gemaakte afspraken, of het de implementatie van één of meer componenten bijzonder (onnodig) moeilijk maakt.
