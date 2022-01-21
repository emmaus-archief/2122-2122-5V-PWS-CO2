# 5V PWS CO2 template
Deze github-repo is onderdeel van een PWS-project voor leerlingen uit 5VWO op het Emmauscollege in Rotterdam

# Uitleg over uitlezen van CO2 waarden
We willen een plattegrond van de school inkleuren met de CO2waarden van die ruimten. Dat willen we op een website plaatsen, zodat mensen voor wie die informatie handig is, dat kunnen bekijken.

Elk lokaal op onze school heeft een CO2-meter. Alle CO2-meters op onze school hebben een QR code.
Die QR code bevat een link naar een webpagina van de leverancier van de CO2-meter.
Die webpagina toont het CO2 niveau.
De webpagina maakt gebruik van een api, via die api worden de CO2-waarden in de webpagina gelezen. 

Als je de html-code van de de webpagina achter een QR-code bekijkt dan vind je dus niet de CO2-waarde. Als je in de ontwikkelmodus de DOM van de website bekijkt dan vind je wel de CO2waarde.

Twee mogelijke oplossingsrichtingen om de waarden van de CO2 meter in onze code te kunnen lezen:
1. Via de api. Technisch is dit de meest chique oplossing. Van de api hebben we helaas geen documentatie en de code in de webpagina die de api raadpleegt is complex (obfuscated), waardoor het lastig is om vanuit de code uit te zoeken hoe het werkt (reverse engineering).

2. Via een door ons gemaakte webpagina waarin we webpagina's van de QR-codes in iframes inladen.
In onze webpagina kunnen we dan met JavaScript uitzoeken wat de waarde van de CO2 is, door de inhoud van het iframe te bekijken.
Hierbij lopen we aan tegen een beveiligingsprobleem op het gebied van cross-origin, maar daarvoor is een work-around (zcross-origin controle in je browser uitzetten)

Optie 2 is werkend gemaakt voor [vangeest](https://github.com/vangeest), maar met dank aan [stemvork](https://github.com/stemvork) werd het mogelijk om toch startcode te maken voor optie 1

## Uitleg hoe de api werkt
Onderzoek om de API van Air-insight te kunnen gebruiken:
- De URL die ik gebruikte was die van C24: (link gaat direct naar grafiek met alle datapunten, in plaats van de informatie zoals de screenshot is) https://www.air-insight.com/rooms/f30251e5-a393-4a06-a661-98f6b1997e8b/detail
- Die pagina heb ik geladen met de Chrome DevTools aan. Ik heb gefilterd op Fetch/XHR en zag twee interessante aanvragen:
    1. https://api.co2.creamcookies.net/hs/hisRead?id=%408a9dd17c-d814-4739-861a-074787b527ed&range=%222022-01-21%22
    - Deze geeft als respons alle waarden (resolutie 5 minuten) als JSON:

```
{
"meta": {"ver":"2.0", "id":"r:8a9dd17c-d814-4739-861a-074787b527ed", "hisStart":"t:2022-01-21T00:00:00+01:00 Amsterdam", "hisEnd":"t:2022-01-22T00:00:00+01:00 Amsterdam"},
"cols":[
{"name":"ts"},
{"name":"val"}
],
"rows":[
{"ts":"t:2022-01-21T00:03:11-00:00 Amsterdam", "val":"n:441"},
{"ts":"t:2022-01-21T00:08:11-00:00 Amsterdam", "val":"n:444"},
{"ts":"t:2022-01-21T00:13:11-00:00 Amsterdam", "val":"n:436"},
{"ts":"t:2022-01-21T00:18:11-00:00 Amsterdam", "val":"n:431"},
...
{"ts":"t:2022-01-21T20:23:07-00:00 Amsterdam", "val":"n:461"},
{"ts":"t:2022-01-21T20:28:07-00:00 Amsterdam", "val":"n:432"}
]
}
```

    - Daarin staat echter een ander id dan in de oorspronkelijke URL. Vermoeden: locatie vs. sensor. Ik was benieuwd waar dit id vandaan kwam.
    2. https://api.co2.creamcookies.net/mapping/co2/f30251e5-a393-4a06-a661-98f6b1997e8b
- Google gaf mij 0,0 informatie over deze romige koekjes API. Dus opende ik een console en begon ik met curl https://api.co2.creamcookies.net/mapping/co2/f30251e5-a393-4a06-a661-98f6b1997e8b. Daar kreeg ik geen respons, ik moest duidelijk headers toevoegen.
- Daarom bestudeerde ik de Headers tab van het tweede request. Door twee headers toe te voegen (content-type en authorization) kreeg ik wel de respons!
    - curl --header 'Content-Type: application/json' --header 'Authorization: lkDvA5MDW8vA#N5XVV8vkvA5AqJX*1' "https://api.co2.creamcookies.net/mapping/co2/f30251e5-a393-4a06-a661-98f6b1997e8b"
    - {"tenantId":"4c4411d0-1947-4fc5-8bd3-08d945fda0ae","value":"8a9dd17c-d814-4739-861a-074787b527ed","parameters":{"devEUI":"A81758FFFE053F42","code":"SD-TE-FD-N9"},"id":"f30251e5-a393-4a06-a661-98f6b1997e8b"}%
- Mogelijk is dat tenantid constant en was dit dus geen wezenlijke stap. Meters maken kan echter nooit kwaad, verkenning gaat zelden via het gebaande pad. Nu nog die data verkrijgen die ik 
- .... YESS! Gelukt!
- curl --header 'Content-Type: application/json' --header 'Authorization: lkDvA5MDW8vA#N5XVV8vkvA5AqJX*1' --header 'x-iot-unica-tenantid: 4c4411d0-1947-4fc5-8bd3-08d945fda0ae' "https://api.co2.creamcookies.net/hs/hisRead?id=%408a9dd17c-d814-4739-861a-074787b527ed&range=%222022-01-21%22"
- Om met de API te praten zijn dus de Authorization en X-IOT-Unica-TentantId waarden nodig. Die laatste lijkt mij constant voor het Emmauscollege en de eerste moet haast wel constant zijn. Ik kan in ieder geval niet vinden waar de requests deze Authorization key vandaan halen "lkDvA5MDW8vA#N5XVV8vkvA5AqJX*1".

# Uitleg over hoe we plattegrond tekenen
Twee opties liggen voor de hand:
1. manipulatie van de DOM via javascript waarbij we gekleurde rechthoeken kunnen positioneren
2. canvas maken waarin we de p5js library gebruiken om te tekenen]

In beide gevallen gebruiken we een plaatje van de plattegrond van onze school op de achtergrond

# Onze site beschikbaar maken

## Uitproberen
Je kunt het template zelf uitproberen in Replit: 
[Run on Repl.it](https://replit.com/github/svgeest/2122-5V-PWS-CO2) <br>
of in Gitpod (gitpod is zonder mailen van orders en zonder permanent hosten):
[Run on Gitpod](https://gitpod.io/#https://github.com/svgeest/2122-5V-PWS-CO2)

## Hosten
Als je de site wilt tonen op een willekeurige computer op een moment dat jij niet ingelogged bent op github of replit, dan kan dat alleen als je de site in replit hebt gezet. Gitpod sluit namelijk de webserver af als je een tijdje niets gedaan hebt. Replit laat de site slapen, maar wekt die binnen enkele seconden als iemand hem opvraagt

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
* basic html & css & javascript reference <br>
https://www.w3schools.com
* replit online editor en hosting ontwikkelomgeving <br>
https://docs.replit.com/
* gitpod online editor en hosting ontwikkelomgeving <br>
https://www.gitpod.io/docs/
* Hoe je opmaak aan je README.md toevoegt <br>
https://www.markdownguide.org/basic-syntax/

# Howto 

## Wat je wilt doen
beschrijving van de exacte stappen om te bereiken wat je wilt

# Credits
- [stemvork](https://github.com/stemvork) voor het reverse-engineren van de api
- [svgeest](https://github.com/vangeest) voor het maken van een repo met startcode




