Deze directory bevat een [json bestand](./lanes.json) die de attributen van verschillende stoplichten en hun rijbanen opslaat.
De belangrijkste informatie hier is welke banen een intersectie hebben met elkaar. 
(De stoplichten van deze banen zouden nooit op hetzelfde moment op groen mogen staan)

Dit bestand is gemaakt op basis van dit mooie kaartje van Danny:
![Intersectie met legenda](../assets/intersection.png)

En dit nog mooiere kaartje van Jorrit:
![Intersectie brug, met legenda](../assets/Dammelaan%20brug%20taffic%20light%20overview.png)

# Wat heb ik hier aan?
Dit bestand is gemaakt met een implementatie van een *controller* als target audience, maar kan ook handig zijn voor de simulatie.
Je kan dit bestand in je controller inladen zodat deze de informatie over de rijbanen kan gebruiken bij besluitvorming.
Dit zodat niet elk groepje hoeft te hard-coden welke baan intersect met welke andere banen.

Advies om dit bestand één keer te parsen bij het opstarten van je controller, en om te zetten naar een object die native is aan je programmeertaal.

Er zijn twee manieren voor je controller om dit bestand te bereiken:
1. Je kopieert en plakt het [json bestand](./lanes.json) naar een bestand die je progamma lokaal kan inlezen. (of naar de broncode van het programma zelf, ik kijk niet mee)
2. Je programma load het bestand live van ``https://raw.github.com/jorrit200/stoplicht-communicatie-spec/main/intersectionData/lanes.json`` elke keer dat het opstart.

Als je het bestand lokaal plaatst weet je zeker dat je controller zich altijd hetzelfde zal gedragen. 
Maar als je het live ophaalt, en je een goede parser schrijft, heb je een controller die zich automatisch aanpast aan de afgesproken standaard

# Spec
Voor exact format, raadpleeg [het schema](./lanes_schema.json). Deze veranderd nooit, tenzij absoluut noodzakelijk

Elk stoplicht bestaat uit een id met het format `"g.l"` waar `g` voor de stoplichtgroep staat, en `l` voor de specifieke baan (lane).
De meeste eigenschappen van een stoplicht die dit document specificeert gelden voor de hele groep (dus gelijk voor elke baan in de groep).
Het json bestand beschrijft een aantal properties van deze stoplichten, en in een paar gevallen een aantal specifieke properties per baan (lane)

## Groups
Het top-level object bevat een "groups" object (`$.groups`), dit is een object met een key voor elke stoplichtgroep.
Elk object bevat de volgende properties:
- `intersects_with` `int[]` Beschrijft met welke andere stoplichtgroepen deze groep intersectie heeft. Als je deze groep, en een groep in deze lijst op groen zet, kun je een collisie veroorzaken.
- `is_invese_of` `int|false` Als 2 stoplichten over een 2-richtingsbaan gaan, waar bij één verantwoordelijk is voor de ene richting, en de ander verantwoordelijk voor de andere richting, dan zijn deze stoplichten elkaars inverse. Een int verwijst naar een andere stoplicht groep, false betekent dat deze groep geen inverse heeft. Let op: dit geld alleen als het over fysiek dezelfde baan gaat, dus 21 en 22 zijn wel inverse van elkaar, maar 2 en 8 niet. Het gaat hier om 2 richtingsbanen, dus stoplichten die elkaars inverse zijn kunnen *wel* tegelijkertijd op groen staan (tenzij ze ook een intersectie met elkaar hebben, zoals bij 71 en 72)
- `extends_to` `int[]|false` Als het verkeer van één stoplicht altijd bij een ander stoplicht terecht komt staat dat hier vermeld. Een int array betekent dat het verkeer van dit stoplicht gegarandeerd bij één van de vermelde stoplicht-groepen komt. false betekent dat er niet vast gesteld kan worden bij welke andere stoplichten het verkeer komt, omdat dit verkeer de simulatie kan verlaten.
- `vehicle_type` `("walk"|"bike"|"car"|"boat")[]` Beschrijft wat voor voertuigen rijden op deze banen, en reageren op deze stoplichtgroep.
- `lanes` `{l: lane}` Een object die de properties per baan beschrijft, in plaats van per groep. Bevat een key per baan (`l`) in deze groep (`g`). Elke waarde bevat alleen informatie die afwijkt van de informatie in de groep (`g`), een leeg object `{}` betekent dus dat die baan (`l`) bestaat en alle properties overeenkomen met de groep.
- `is_physical_barrier` `bool` Als dit "stoplicht" op rood staat, is het fysiek onmogelijk om door te rijden. Dit is relevant voor de simulatie: als een voertuig onwettig gedrag heeft, of een hulpdienstvoertuig kan besluiten door rood te rijden. Dat kan bij dit stoplicht dus niet.

Voorbeeld:
```json
"2": {
      "intersects_with": [5, 6, 9, 10, 11, 12, 21, 26, 31, 36],
      "is_inverse_of": false,
      "extends_to": false,
      "vehicle_type": ["car"],
      "lanes": {"1": {}, "2": {}},
      "is_physical_barrier": false
    },
```
Beschrijft groep 2, met stoplicht `2.1` en `2.2`, die geen afwijking hebben van de rest van de groep. 
Deze rijbaan groep is niet de inverse van een andere, en lijdt niet tot een specifieke andere rijbaan-groep. 
Er rijden alleen auto's op deze banen. Deze baan heeft een stoplicht die geen fysieke barrière representeert. 


## lanes
In de meeste gevallen is het genoeg om alleen naar de group objecten te kijken, want de meeste properties (en de belangrijkste) gelden altijd voor de hele groep.
Maar de properties: `is_inverse_of`, en `extends_to` kunnen afwijken per lane (`l`).

Per baan (`l`) verwijzen deze properties naar andere banen, in tegenstelling tot dezelfde properties in groepen die naar hele groepen verwijzen. 
Daarom gebruiken deze properties het `string` type, waar dezelfde properties in een group object het `int` type gebruiken, met het format `"g.l"` waar `g` naar de group verwijst, en `l` naar de lane.

Lane objecten *kunnen* de volgende properties hebben:
- `is_inverse_of` `string` deze baan is de inverse vaan de aangegeven andere baan.
- `extends_to` `string` deze baan lijdt alleen maar naar de aangegeven andere baan.

voorbeeld:
```json
"31": {
      "intersects_with": [1, 2, 3],
      "is_inverse_of": false,
      "extends_to": false,
      "vehicle_type":["walk"],
      "lanes": {
        "1": {
          "is_inverse_of": "31.2",
          "extends_to": "32.1"
        },
        "2": {
          "is_inverse_of": "31.1"
        }
      },
      "is_physical_barrier": false
    },
```
Dit is een group met 2 lanes die elkaars inverse zijn. 
`31.1` En `31.2` zijn dus 2 stoplichten die over dezelfde lane gaan, 
maar `31.1` gaat over de zuid-richting en `32.2` gaat over de noord-richting (zie [intersectie figuur](../assets/intersection.png)).
Deze banen hebben geen intersectie met elkaar (banen in dezelfde groep kunnen geen intersectie met elkaar hebben, en loop verkeer is altijd 2-richtingsverkeer),
en kunnen dus tegelijkertijd op groen staan, maar het gebeurt ook dat er maar één op groen staat in de praktijk.
Lane `31.1` lijdt naar lane `32.1`.

# Ik haat json parsen
Dan heb ik hier dit mooie intersectie tabelletje:

| 🚦 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 21 | 22 | 23 | 24 | 25 | 26 | 27 | 28 | 31 | 32 | 33 | 34 | 35 | 36 | 37 | 38 | 41 | 42 | 51 | 52 | 53 | 54 | 61 | 62 | 63 | 64 | 71 | 72 |
|----|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|----|
| 1  |   |   |   |   | x |   |   |   | x |    |    |    | x  |    |    |    |    |    |    | x  | x  |    |    |    |    |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |
| 2  |   |   |   |   | x | x |   |   | x | x  | x  | x  | x  |    |    |    |    | x  |    |    | x  |    |    |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 3  |   |   |   |   | x | x | x | x |   |    | x  |    | x  |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 4  |   |   |   |   |   |   |   | x |   |    |    | x  |    | x  | x  |    |    |    |    |    |    | x  | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 5  | x | x | x |   |   |   |   | x | x |    |    | x  |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |
| 6  |   | x | x |   |   |   |   | x | x | x  | x  | x  |    |    | x  |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 7  |   |   | x |   |   |   |   |   |   |    | x  |    |    |    |    | x  | x  |    |    |    |    |    |    | x  | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 8  |   |   | x | x | x | x |   |   |   |    | x  | x  |    | x  |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 9  | x | x |   |   | x | x |   |   |   |    | x  | x  |    |    |    |    | x  |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |
| 10 |   | x |   |   |   | x |   |   |   |    |    |    |    |    |    |    |    | x  | x  |    |    |    |    |    |    | x  | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 11 |   | x | x |   |   | x | x | x | x |    |    |    |    |    |    | x  |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 12 |   | x |   | x | x | x |   | x | x |    |    |    |    | x  |    |    |    |    | x  |    |    | x  |    |    |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 21 | x | x | x | x |   |   |   | x |   |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 22 | x | x | x | x |   |   |   | x |   |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 23 |   |   | x | x | x | x | x |   |   |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 24 |   |   | x | x | x | x | x |   |   |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 25 |   | x |   |   |   | x | x | x | x | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 26 |   | x |   |   |   | x | x | x | x | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 27 | x |   |   |   | x |   |   |   | x | x  | x  | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 28 | x |   |   |   | x |   |   |   | x | x  | x  | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 31 | x | x | x |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 32 |   |   |   | x |   |   |   | x |   |    |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 33 |   |   |   | x | x | x |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 34 |   |   | x |   |   |   | x |   |   |    | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 35 |   |   |   |   |   |   | x | x | x |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 36 |   | x |   |   |   | x |   |   |   | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 37 |   |   |   |   |   |   |   |   |   | x  | x  | x  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 38 | x |   |   |   | x |   |   |   | x |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
| 41 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 42 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 51 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 52 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 53 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 54 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 61 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 62 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 63 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 64 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  |
| 71 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  | x  | x  | x  | x  | x  | x  | x  | x  |    | x  |
| 72 |   |   |   |   |   |   |   |   |   |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    | x  | x  | x  | x  | x  | x  | x  | x  | x  | x  | x  |    |


En dan mag je dat lekker handmatig gaan implementeren in je controller.