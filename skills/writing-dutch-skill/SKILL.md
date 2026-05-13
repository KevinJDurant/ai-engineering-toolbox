---
name: writing-dutch-skill
description: Gebruik deze skill wanneer de gebruiker vraagt om Nederlandse tekst te schrijven, herschrijven, redigeren, menselijker te maken of te polijsten zodat die natuurlijk, specifiek en menselijk klinkt. Vooral geschikt voor proza, documentatie, e-mails, essays, marketingteksten, bio's, socialmediaberichten, UI-teksten en andere Nederlandse tekst waarbij stem en leesbaarheid belangrijk zijn.
---

# Writing Dutch Skill

Herschrijf Nederlandse tekst zodat die klinkt alsof die door een mens is geschreven, niet door een model.

## Gebruik deze skill wanneer

- De gebruiker vraagt om Nederlandse tekst natuurlijker, vlotter of minder AI-achtig te maken.
- De tekst opgeblazen, generiek, promotioneel of te gladgestreken aanvoelt.
- De gebruiker een herschrijving wil die de betekenis bewaart maar de stem en het ritme verbetert.
- De gebruiker een schrijfstijlvoorbeeld geeft en wil dat de herschrijving daarop aansluit.
- De taak is om tekst in het Nederlands te schrijven of te herschrijven en die natuurlijk moet klinken.

## Kerntaak

1. Bewaar de onderliggende betekenis.
2. Vermijd duidelijke AI-achtige schrijfpatronen.
3. Sluit aan op de bedoelde toon en doelgroep.
4. Voeg genoeg persoonlijkheid, concreetheid en ritme toe zodat de tekst echt en doorleefd aanvoelt.
5. Doe een laatste interne controle: vraag wat nog AI-achtig overkomt en herzie daarna nog een keer.

## Werkwijze

### 1. Stem de toon af

Als de gebruiker een schrijfstijlvoorbeeld geeft, volg dat dan nauwgezet:

- zinslengte en ritme
- woordgebruikniveau
- interpunctiegewoonten
- alinea-openingen en overgangen
- terugkerende formuleringen of verbale trekjes

Als er geen voorbeeld is, kies dan standaard voor proza dat direct en gevarieerd is, en waar passend een tikje uitgesproken.

### 2. Vermijd veelvoorkomende AI-achtige patronen

Gebruik deze patronen als schrijfchecklist. Het doel is niet om AI-gebruik te detecteren, maar om tekst te schrijven die niet aanvoelt als generieke modeloutput. Los het onderliggende schrijfprobleem op, zoals vaagheid, opgeblazen taal, ongefundeerde toeschrijving, generieke formuleringen of een te glad ritme, in plaats van alleen synoniemen te wisselen:

- Opgeblazen belang: "cruciaal," "een bewijs van," "onderstreept," "bredere trend," "blijvende impact"
- Promotionele toon: "levendig," "gerenommeerd," "verbluffend," "baanbrekend," "in het hart van"
- Vage autoriteiten: "experts zeggen," "waarnemers merken op," "sectorrapporten" zonder concrete bron
- Lege constructies: "benadrukkend," "weerspiegelend," "bijdragend aan," "etaleren"
- Tutorial-aankondigingen: "Laten we erin duiken," "dit moet je weten," "laten we dit verkennen"
- Formulematige structuren: "niet alleen X, maar ook Y," drietallen, valse spanningsbogen, symmetrische contrasten
- AI-achtig woordgebruik: "cruciaal," "zich verdiepen in," "landschap," "samenspel," "uitlichten," "bevorderen"
- Vermijden van gewone koppelwerkwoorden en opgeblazen werkwoorden: "fungeert als," "doet dienst als" waar `is` of `heeft` schoner is
- Afzwakken en keel schrapen: "men zou kunnen stellen," "hoewel details beperkt zijn," "tot slot"
- Overmatig gebruik van gedachtestreepjes, enthousiaste aanhalingstekens, vetgedrukte nadrukpunten, emoji-opsommingen, nette kopjesachtige fragmenten
- Generieke positieve afsluiters: "de toekomst ziet er rooskleurig uit," "spannende tijden liggen in het verschiet," "deze reis gaat verder"
- Uniform ritme: elke zin even lang, dezelfde cadans, hetzelfde niveau van afwerking

### 3. Vervang ze door menselijke signalen

Kies liever voor:

- concrete zelfstandige naamwoorden en directe werkwoorden
- concreetheid boven abstractie
- een ongelijk maar gecontroleerd ritme
- eenvoudige zinsbouw wanneer dat beter leest
- milde mening, onzekerheid of scherpte wanneer de context dat toelaat
- de eerste persoon alleen wanneer die bij genre en gebruikersdoel past

### 4. Houd de stem passend

- Technisch schrijven moet precies zijn, niet te los of praatgraag.
- Professioneel schrijven mag warm zijn zonder als marketing te klinken.
- Informeel schrijven mag ruwer, korter en minder symmetrisch zijn.
- Als het origineel formeel moet blijven, voeg dan geen plat taalgebruik toe alleen om menselijker te lijken.

## Redactieheuristieken

- Schrap de zin als die alleen aankondigt wat de volgende zin gaat zeggen.
- Vervang abstracte samenvattingszinnen waar mogelijk door een concrete uitspraak.
- Breek te gladgestreken alinea's open.
- Voeg korte opvulzinnen samen als ze een ingestudeerd ritme opleveren.
- Kies liever een scherp voorbeeld dan drie generieke beweringen.
- Verwijder schijnbalans waar de tekst doet alsof beide kanten worden afgewogen maar weinig zegt.

## Output

Standaardgedrag:

- Geef eerst de herschreven tekst terug.
- Voeg indien nuttig een korte noot toe over de grootste wijzigingen.

Als de gebruiker expliciet analyse wil, geef dan:

1. een korte analyse van de duidelijkste AI-achtige patronen
2. de herschreven versie

Forceer geen lange uitleg als de gebruiker alleen natuurlijkere tekst wil.

## Randvoorwaarden

- Verzin geen bronnen, mensen, quotes of feiten om de tekst menselijker te laten aanvoelen.
- Maak ongefundeerde claims niet alleen natuurlijker; schrap, nuanceer of markeer ze.
- Verwijder geen noodzakelijke technische details.
- Maak formele of juridische tekst niet achteloos subjectief tenzij de gebruiker daar expliciet om vraagt.
- Maak de betekenis niet vlakker alleen om de tekst korter te maken.
- Voeg geen opzettelijke fouten, houterigheid of nep-onvolmaaktheid toe om AI-detectors te omzeilen.

## Referentie

Deze skill is voor Codex aangepast op basis van `blader/humanizer` en de Wikipedia-gids over signalen van AI-schrijfwerk.
