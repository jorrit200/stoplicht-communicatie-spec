# Spec - sensoren speciaal
- Publisher: controller 
- Subscriber: simulatie

## Publisher
De controller kan op elk moment de status van een stoplicht besluiten en publiceren.

## Subscriber
De simulatie moet het gedrag van verkeersdeelnemers bepalen op basis van relevant stoplicht.

## Regels
- De status van een stoplicht kan zijn: "groen", "oranje" of "rood"
- Elk stoplicht heeft een status
- De start status van elk stoplicht (voor dat er een bericht is gestuurd) is "rood"