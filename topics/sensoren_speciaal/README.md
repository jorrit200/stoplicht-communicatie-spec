# Spec - sensoren speciaal

- Publisher: simulatie
- Subscriber: controller

## Publisher

### Voertuigsensoren

De simulatie published de staat van elke speciale sensor, wanneer deze staat verandert.

De start status van de brug status sensor op `false`.

Speciale sensoren worden geïdentificeerd met een naam.
De staat van deze sensor kan `true` of `false` zijn.

De sensor is `true` als er één of meerdere voertuigen zich in het sensorgebied bevinden, anders `false`

De speciale brug status sensoren zijn:

- brug_wegdek
- brug_water
- brug_file

### Brug opening

Tevens hebben we de brug openings-sensor: `brug_opening`

Deze heeft als start status `"dicht"`, dan staat de brug dus dicht en kunnen er auto's overheen rijden.

De status van deze sensor kan `"open"`,`"dicht"` of `"onbekend"` zijn.

Als de brug volledig open staat, *Lees: er kan een boot doorheen varen*, dan is de state `"open"`

Als de brug volledig dicht is, *Lees: er kunnen auto's overheen rijden*, dan is de state `"dicht"`

Als de brug aan het openen of sluiten is, of in enig andere staat welke niet `"open"` of`"dicht"` is, is de status `"onbekend"` 

## Subscriber

De controller ***M O E T*** de staat van deze sensoren gebruiken, om te kijken of het mogelijk is specifieke "stoplichten" van staat te veranderen.

De regels waar de controller rekening mee moet houden zijn inbegrepen bij [intersectionData](../../intersectionData/README.md)

## Regels

- Dit topic bestaat uit alle sensoren die niet direct aan een rijbaan/stoplicht te koppelen valt.