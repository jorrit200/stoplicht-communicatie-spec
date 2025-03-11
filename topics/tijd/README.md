# Spec - tijd
- Publisher: simulatie 
- Subscriber: controller

## Publisher
De simulatie bepaald de simulatietijd in milliseconden. Dit is de tijd sinds het opstarten van de simulatie. 
De snelheid van deze tijd mag afwijken van de realiteit. (Bijvoorbeeld 10 simulate seconden per seconde)  

## Subscriber
De controller moet deze tijd aanhouden voor tijdsgebonden gedrag.
Bijvoorbeeld het maximaal aantal seconden dat een stoplicht op groen mag staan.

## Onderbouwing
De simulate hoeft niet in realtime te draaien. Op deze manier past de controller zich automatisch aan op de tijd die de simulatie besluit aan te houden.
De simulatie is logischerwijs de verantwoordelijk voor het aangeven van de gesimuleerde tijd.
Op deze manier loopt de tijd altijd synchroon, ook bij onverwachte vertragingen.

## Regels
- De simulatie tijd wordt aangegeven in msa
- De simulatie tijd wordt minstens 1x per 100 simulatie ms gepublished 