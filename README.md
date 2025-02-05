# stoplicht-communicatie-spec
Dit document beschrijft de communicatie tussen de kruispuntsimulatie en de controller van het stoplichtprobleem.

### volgorde van acties
- de simulatie stuurt haar staat in JSON naar de controller.
- de controller bepaald wat de verkeerslichten moeten doen.
- de controller stuurt die gewenste staat naar de simulator.

*de regressiemeter is geen middle man en luistert mee.*

### problemen met deze oplossing?
- maak een issue met de repo.
- communiceer het naar de anderen in discord of de lessen.
- veranderingen worden democratisch behandeld

Over tijd heen zal deze spec specifieker in gaan op het JSON format.
