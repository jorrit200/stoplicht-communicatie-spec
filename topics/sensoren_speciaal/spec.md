# Spec - sensoren speciaal
- Publisher: simulatie
- Subscriber: controller

## Publisher
De simulatie published de staat van elke speciale sensor, wanneer deze staat verandert.

De start status is elke sensor op `false`.

Speciale sensoren worden geïdentificeerd met een naam.
De staat van deze sensor kan `true` of `false` zijn.

De sensor is `true` als er één of meerdere voertuigen zich in het sensorgebied bevinden, anders `false`

De speciale sensoren zijn:
- brug_wegdek
- brug_water
- brug_file


## Subscriber
De controller ***M O E T*** de staat van deze sensoren gebruiken, om te kijken of het mogelijk is specifieke "stoplichten" van staat te veranderen.

De regels waar de controller rekening mee moet houden zijn inbegrepen bij [intersectionData](../../intersectionData/README.md)


## Regels
- Dit topic bestaat uit alle sensoren die niet direct aan een rijbaan/stoplicht te koppelen valt.