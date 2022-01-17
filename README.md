# 5V PWS CO2 template
Deze github-repo is onderdeel van een PWS-project voor leerlingen uit 5VWO op het Emmauscollege in Rotterdam

# Basis uitleg hoe het werkt
We willen een plattegrond van de school inkleuren met de CO2waarden van die ruimten. Dat willen we op een website plaatsen, zodat mensen voor wie die informatie handig is, dat kunnen bekijken.

Elk lokaal op onze school heeft een CO2-meter. Alle CO2-meters op onze school hebben een QR code.
Die QR code bevat een link naar een webpagina van de leverancier van de CO2-meter.
Die webpagina toont het CO2 niveau.
De webpagina maakt gebruik van een api, via die api worden de CO2-waarden in de webpagina gelezen. 

Als je de html-code van de de webpagina achter een QR-code bekijkt dan vind je dus niet de CO2-waarde. Als je in de ontwikkelmodus de DOM van de website bekijkt dan vind je wel de CO2waarde.

Van de api hebben we helaas geen documentatie en de code in de webpagina die de api raadpleegt is complex (obfuscated), waardoor het lastig is om vanuit de code uit te zoeken hoe het werkt (reverse engineering).

Een mogelijke oplossing is dat we alle QR-codes verzamelen en een webpagina maken waarin we webpagina's van de QR-codes in iframes inladen.
In onze index.html kunnen we dan met JavaScript uitzoeken wat de waarde van de CO2 is, door de inhoud van het iframe te bekijken.
Hierbij lopen we aan tegen een beveiligingsprobleem op het gebied van cross-origin, maar daarvoor is een work-around (zie verderop)

# Onze site beschikbaar maken

## uitproberen
Je kunt het template zelf uitproberen in Replit: 
[Run on Repl.it](https://replit.com/github/emmauscollege/5HV-webshop-template) <br>
of in Gitpod (gitpod is zonder mailen van orders en zonder permanent hosten):
[Run on Gitpod](https://gitpod.io/#https://github.com/emmauscollege/5HV-webshop-template)

## hosten
Als je de site wilt tonen op een willekeurige computer op een moment dat jij niet ingelogged bent op github of replit, dan kan dat alleen als je de site in replit hebt gezet. Gitpod sluit namelijk de webserver af als je een tijdje niets gedaan hebt. Replit laat de site slapen, maar wekt die binnen enkele seconden als iemand hem opvraagt

## browser instellen
Deze webpagina werkt alleen als je de cross-origin controle in je browser uitzet.
Dat heeft wel beveiligingsrisico's, dus het is aan te raden om die weer aan te zetten voordat je obscure sites bekijkt.
Als je de cross-origin check niet uitzet in je browser, dan kan de code uit index.html niet bij de iframes waarin de webpagina's achter de QR code op de CO2 meters worden geladen. Je ziet dan cross-origin errors in de console van je browser als je index.html laadt.


# Plan van aanpak
Werk de planning af van boven naar beneden<br>
Geef aan met [x] welke onderdelen af zijn

Stap 1: Wat moet het kunnen? 
- [ ] Wie gaan het gebruiken?
- [ ] Waarvoor gaan die mensen het gebruiken?
- [ ] Welke functionaliteiten zouden die mensen willen?

Stap 2: Welke technieken kun je gebruiken?
- [ ] Startcode van docent voor inlezen van 1 sensor wel/niet gebruiken 
- [ ] Plattegrond maken met CSS of met P5JS library
- [ ] Wel of geen backend met database gebruiken
- [ ] ...

Stap 3: Keuzes: hoe ga je het maken en welke functies kun je daarmee maken (prioriteer)? 
- [ ] ...

Stap 4: Eerste versie met gebruikers checken
- [ ] Check
- [ ] (her)prioriteer
- [ ] Maak

Stap 5: Tweede versie met gebruikers checken
- [ ] Check
- [ ] (her)prioriteer
- [ ] Maak

...

# Documentatie 
* lesmateriaal over website
* lesmateriaal over webshop
* basic html & css & javascript reference<br>
https://www.w3schools.com
* replit online editor en hosting ontwikkelomgeving<br>
https://docs.replit.com/
* gitpod online editor en hosting ontwikkelomgeving<br>
https://www.gitpod.io/docs/

# Howto 

## Wat je wilt doen
beschrijving van de exacte stappen om te bereiken wat je wilt

# Credits
- Zet hier wie je geholpen hebben




