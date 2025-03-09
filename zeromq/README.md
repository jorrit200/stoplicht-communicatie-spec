# ZeroMQ message patterns
In dit document worden ZeroMQ berichtpatronen en hoe deze mogelijk in het project gebruikt kunnen worden uitgewerkt.
## Beschikbare patterns
 
- Request-Reply
- Pub-Sub
- Pipeline
- Exclusive Pair
- Dealer - Dealer
- Router - Router

### Request - Reply

Bij dit patroon sturen de sockets om de beurt naar elkaar.<br>Dit kan mogelijk gebruikt worden om de state op te vragen.<br>Zie [hier](https://zeromq.org/socket-api/?language=c&library=libzmq#request-reply-pattern) voor de documentatie

| Socket | Richting | Framing | Notities |
| :- | :-: | :- | :- |
| [REQ](https://zeromq.org/socket-api/?language=c&library=libzmq#req-socket) | TX | DIY Framing | Kan niks sturen als er nog een reply verwacht wordt |
| [REP](https://zeromq.org/socket-api/?language=c&library=libzmq#rep-socket) | RX | DIY Framing | Kan niks sturen als er nog een request verwacht wordt |

<sub>Tabel 1 <br> Sockets van Request - Reply patroon</sub>

#### Mogelijk gebruik voor project

![Figuur 1 Voorbeeld Request - Reply architectuur](../assets/zeromq/req-rep.png)

<sub>Figuur 1<br>Voorbeeld Request - Reply architectuur</sub>

De simulator kan met een REQ socket bij de REP socket van de controller vragen (met de data van de simulatie state) om de state van alle stoplichten. De controller geeft dit dan als een reply terug.<br>
Voor de regressie tester wordt de data die de controller en simulator binnenkrijgen direct doorgestuurd via een extra PUSH naar de regressie tester, deze moet dan vervolgens met een leeg bericht antwoorden op de REP.

### Publish - Subscribe

Bij dit patroon verbindt een SUB socket op een PUB socket om data op diverse topics te ontvangen.<br>Zie [hier](https://zeromq.org/socket-api/#publish-subscribe-pattern) voor de documentatie.

| Socket | Richting | Framing | Notities |
| :- | :-: | :- | :- |
| [PUB](https://zeromq.org/socket-api/#pub-socket) | TX | Multipart, eerst topic en daarna data |
| [SUB](https://zeromq.org/socket-api/#sub-socket) | RX | Zoals PUB stuurt | Verstuurt niks naar PUB
| [XPUB](https://zeromq.org/socket-api/#xpub-socket) | TX | Net zoals PUB | Ontvangt subscription requests van XSUB. Ondersteunt ook SUB.
| [XSUB](https://zeromq.org/socket-api/#xsub-socket) | RX | Net zoals PUB | Stuurt een subscription

<sub>Tabel 2<br>Sockets van Publish - Subscribe patroon</sub>


#### Mogelijk gebruik voor project

![Figuur 2 Voorbeeld Publish - Subscribe architectuur](../assets/zeromq/pub-sub.png)

<sub>Figuur 2<br>Voorbeeld Publish - Subscribe architectuur</sub>

De simulator en controller kunnen beide een PUB en een SUB socket openen, waarna ze via hun SUB sockets op elkaars PUB sockets verbinden om zo naar elkaar berichten naar elkaar te sturen en van elkaar te ontvangen.<br>
Hierbij kan bijvoorbeeld gebruik gemaakt worden van topics zoals `simulator/SensorUpdate` of `controller/LightsUpdate`.<br>
De regressietester kan alle berichten afluisteren door een subscriptie af te nemen op zowel de controller als de simulator.

### Pipeline

Bij dit patroon wordt er van de PUSH socket gegevens naar PULL sockets gestuurd.<br>Dit patroon is vergelijkbaar met Request-Reply, alleen komt er geen antwoord terug van de clients.<br>Zie [hier](https://zeromq.org/socket-api/#pipeline-pattern) voor de documentatie.

| Socket | Richting | Framing | Notities |
| :- | :-: | :- | :- |
| [PUSH](https://zeromq.org/socket-api/#push-socket) | TX | DIY | |
| [PULL](https://zeromq.org/socket-api/#pull-socket) | RX | DIY | |

<sub>Tabel 3<br>Sockets van Pipeline patroon</sub>

#### Mogelijk gebruik voor project

![Figuur 3 Voorbeeld Pipeline architectuur](../assets/zeromq/pipeline.png)

<sub>Figuur 3<br>Voorbeeld Pipeline architectuur</sub>

De simulator en controller kunnen net zoals bij het Publish-Subscribe patroon beide een PUSH en een PULL socket openen, en van de PUSH socket naar elkaars PULL socket de berichten te sturen.<br>
Hierbij is het niet mogelijk om van beide kanten direct antwoord terug te geven, behalve als de kant zijn eigen PUSH socket gebruikt om antwoord te bieden.<br>
De regresietester kan alle berichten afluisteren door met twee PULL sockets op de controller en simulator te verbinden.

### Exclusive Pair

Bij het exclusive pair patroon verbinden twee PAIR sockets met elkaar en kunnen beide frames versturen en ontvangen.<br>
Dit patroon is eigenlijk bedoeld voor in-process communicatie tussen threads. Als een PAIR socket verbindt op een andere PAIR die al een verbinding heeft, dan dropt deze PAIR de bestaande verbinding om de nieuwe verbinding te accepteren.

| Socket | Richting | Framing | Notities |
| :- | :-: | :- | :- |
| [PAIR](https://zeromq.org/socket-api/#pair-socket) | TX/RX | DIY | |

<sub>Tabel 4<br>Sockets van Exclusive Pair patroon</sub>

#### Mogelijk gebruik voor project

![Figuur 4 Voorbeeld Exclusive Pair Architectuur](../assets/zeromq/exclusive-pair.png)

<sub>Figuur 4<br>Voorbeeld Exclusive Pair architectuur</sub>

Hierbij kan hetzelfde patroon gehanteerd worden als bij Request-Reply omdat deze sockets ook werken over de andere zeromq transporten buiten `inproc`. Er moet hier wel gelet worden op de nadelen die bij de PAIR sockets komen.<br>
Bij dit patroon moeten er 4 sockets zijn op de controller en simulator en 2 op de regressie tester om deze onderling te verbinden. 

### Dealer - Dealer

Dit patroon is vergelijkbaar met Request-Reply, alleen is het ontvangen en versturen van frames asynchroon en wacht de DEALER dus niet op antwoord. Ook zijn er minder sockets nodig, omdat DEALER sockets op meerdere sockets kunnen verbinden of verbinding van krijgen.

| Socket | Richting | Framing | Notities |
| :- | :-: | :- | :- |
| [DEALER](https://zeromq.org/socket-api/#dealer-socket) | TX/RX | DIY | Verstuurt asynchroon |

<sub>Tabel 5<br>Sockets van Dealer - Dealer patroon</sub>

#### Mogelijk gebruik voor project

![Figuur 5 Voorbeeld Dealer - Dealer architectuur](../assets/zeromq/dealer-dealer.png)

<sub>Figuur 5<br>Voorbeeld Dealer - Dealer architectuur</sub>

Dit patroon is vergelijkbaar aan normale sockets, maar het versturen is multicast naar alle verbonden clients/servers.<br>
Bij dit patroon kan de regressie tester niet out of box zien waar berichten vandaan komen.

### Router - Router

Dit patroon is vergelijkbaar met Dealer - Dealer, alleen wordt er bij het ontvangen van berichten een frame toegevoegd die identificeert waar het bericht vandaan komt.

| Socket | Richting | Framing | Notities |
| :- | :-: | :- | :- |
| [ROUTER](https://zeromq.org/socket-api/#router-socket) | TX/RX | Multipart, identificatie + data | Verstuurt asynchroon |

<sub>Tabel 6<br>Sockets van Router - Router patroon</sub>

#### Mogelijk gebruik voor project

![Figuur 6 Voorbeeld Router - Router architectuur](../assets/zeromq/router-router.png)

<sub>Figuur 6<br>Voorbeeld Router - Router architectuur</sub>

De gebruikswijze is vergelijkbaar met het Dealer - Dealer patroon, alleen kan de regressie tester wel zien waar het bericht vandaan komt omdat de ROUTER socket dit toevoegt aan een bericht bij het ontvangen. 