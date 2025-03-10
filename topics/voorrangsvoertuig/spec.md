# Spec - voorrangsvoertuig
- Publisher: simulatie
- Subscriber: controller

## Publisher
De simulatie geeft aan dat er een voertuig met voorrang (bijvoorbeeld politie) gebruik moet maken van een bepaalde baan.
De simulatie geeft aan wanneer dit gebeurt. De simulate trekt dit weer in wanner het voertuig niet meer relevant is.

## Subscriber
De controller geeft dit voertuig zo snel als mogelijk (zonder ongelukken te veroorzaken, en rekeninghoudend met eerdere voorrangsvoertuigen) voorrang.

## Regels
- Voorrang onderling voorrangsvoertuigen ligt bij eigen implementatie.
- De aangeven tijd is de tijd dat het voertuig relevant wordt voor de verkeerssituatie.
- De aangeven tijd is in simulatie milliseconden