# Spec - sensoren rijbaan
- Publisher: simulatie
- Subscriber: controller

## Publisher
De simulatie published de staat van elke rijbaan sensor, wanneer deze staat verandert.

De start status is elke sensor op `false`.

Elke rijbaan heeft een voor en achter sensor, hoewel bij sommige rijbanen (voetganger en fietser) de achterste altijd op `false` zal blijven. (Dit is voor gemak bij implementatie logica controller)


## Subscriber
De controller kan de staat van de rijbaansensoren gebruiken voor het optimaliseren van verkeersstroming.


## Regels
- Dit topic bestaat uit een object met een key voor elk stoplicht id (`"g.l"`).
- Elke rijbaan heeft een voor en achter sensor, ook als de achterste niet gebruikt wordt (voetgangers) (dan staat achter altijd op `false`)
- Dit topic bevat alleen sensoren die direct aan een stoplicht id te verbinden zijn (zie [speciale sensoren](../sensoren_speciaal/README))