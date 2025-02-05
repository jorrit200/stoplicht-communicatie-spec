# stoplicht-communicatie-spec
Dit document beschrijft de communicatie tussen het kruispunt simulatie en de controller van het stoplicht probleem.

Het meeest voor de hand liggende is dat de simulatie zijn volledige staat in JSON naar de controller stuurt. De controller kan vervolgens identificeren wat er mis met de staat van de stoplichten (en brug), en de nieuwe staat terug sturen.

Idereen die een probleem heeft met deze spec, kan een issue aanmaken in deze repository, en deze kan vervolgens democratisch behandeld worden. 
Over tijd heen zal deze spec specifieker in gaan op het JSON format.
