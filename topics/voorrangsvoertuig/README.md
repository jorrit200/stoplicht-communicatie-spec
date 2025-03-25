# Spec - voorrangsvoertuig

- Publisher: simulatie
- Subscriber: controller

## Publisher

De simulatie geeft aan dat er een voertuig met voorrang (bijvoorbeeld politie) gebruik moet maken van een bepaalde baan.
De simulatie geeft aan wanneer dit gebeurt. De simulate trekt dit weer in wanner het voertuig niet meer relevant is.

### Voorangstypes

I.v.m. het openbaar vervoer hebben we 2 voorangsprioriteiten:

- Prio 1 is voor voorangsvoertuigen, deze hebben ten alle tijden voorrang en alles op het verkeersplein moet zo worden geregeld dat deze zo snel mogelijk door kunnen.

- Prio 2 is voor openbaar vervoer, deze hebben voorrang op de wachtrij, maar zullen niet het hele verkeersplein op rood zetten om zelf door te kunnen rijden

## Subscriber

De controller geeft dit voertuig zo snel als mogelijk (zonder ongelukken te veroorzaken, en rekeninghoudend met eerdere voorrangsvoertuigen) voorrang.

## Regels

- Voorrang onderling voorrangsvoertuigen ligt bij eigen implementatie.
- De aangegeven tijd is de tijd dat het voertuig relevant wordt voor de verkeerssituatie.
- De aangegeven tijd is in simulatie milliseconden