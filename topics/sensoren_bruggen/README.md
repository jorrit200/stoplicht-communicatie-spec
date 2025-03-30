# Spec - sensoren speciaal
- Publisher: simulatie
- Subscriber: controller

## Publisher
De simulatie published de staat van de brugsensoren, wanneer deze staat verandert.

De start status is elke sensor op `dicht`.

Een sensor word gedefiniëerd met het id en de staat

De status van deze sensor kan `"open"`,`"dicht"` of `"onbekend"` zijn.

Als de brug volledig open staat, *Lees: er kan een boot doorheen varen*, dan is de state `"open"`

Als de brug volledig dicht is, *Lees: er kunnen auto's overheen rijden*, dan is de state `"dicht"`

Als de brug aan het openen of sluiten is, of in enig andere staat welke niet `"open"` of`"dicht"` is, is de status `"onbekend"` 

## Subscriber
De controller ***M O E T*** de staat van deze sensoren gebruiken, om te kijken of het mogelijk is specifieke "stoplichten" van staat te veranderen.