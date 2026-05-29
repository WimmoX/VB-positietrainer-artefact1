# Prompt: 5-1 Volleybal Positietrainer (React-artefact)

Bouw een interactieve React-artefact waarmee een volleybalteam de posities van het **5-1 systeem** kan oefenen. De gebruiker plaatst zijn eigen spelers op een veld en controleert of ze goed staan. Er is geen score en geen winstdoel — puur positioneren en checken. **De interface is volledig in het Nederlands.**

---

## 1. Het veld

- Bovenaanzicht van een volleybalveld, **liggend** (landscape): het net staat **verticaal in het midden**.
- **Jouw team staat op de linkerhelft** (blauwe spelers, interactief). De **tegenstander staat rechts** (rode spelers, niet-interactief, door de app neergezet).
- Zichtbaar: zijlijnen, achterlijnen, 3-meterlijnen aan beide kanten, en het net in het midden.
- **6 positievakken per helft**, subtiel herkenbaar (lichte arcering of dunne scheidslijntjes) — de afbakening mag **niet** visueel overheersen.
- Zet **naast de middellijn een scheidsrechtersstoel** (klein, decoratief — mag een simpel icoontje of emoji 🪑 zijn) voor de sfeer.

### Oriëntatie van de 6 vakken (jouw helft, links)
Het team kijkt richting het net (naar rechts). Vanuit dat perspectief is **rechts = boven in beeld**, **links = onder in beeld**. De achterlijn is de linkerrand, het net is het midden.

| Vak | Naam | Positie in beeld (linkerhelft) |
|-----|------|-------------------------------|
| 1 | rechts-achter | boven, tegen achterlijn |
| 6 | midden-achter | midden-verticaal, tegen achterlijn |
| 5 | links-achter | onder, tegen achterlijn |
| 2 | rechts-voor | boven, tegen net |
| 3 | midden-voor | midden-verticaal, tegen net |
| 4 | links-voor | onder, tegen net |

---

## 2. De spelers

Zes rollen, elk als een sleepbaar (drag-and-drop) rond fiche met label:

- **S** — Setter (visueel onderscheidend, bv. afwijkende kleur zoals oranje)
- **D** — Diagonaal
- **M1** — Midden 1
- **M2** — Midden 2
- **B1** — Buiten 1
- **B2** — Buiten 2

De spelers staan bij start **buiten het veld op een rijtje** (bv. een "bank" onder of links naast het veld). De gebruiker sleept of klikt ze naar hun plek op de linkerhelft.

---

## 3. Rotaties en toestanden

**6 rotaties**, in deze vaste (afwijkende, maar bewust gekozen) volgorde:
> **1 → 6 → 5 → 4 → 3 → 2**

De rotatieknoppen staan bovenin in een rij; standaard begint de app op rotatie 1.

**7 toestanden** per rotatie:

1. **Basis** — spelers staan midden in hun rotatievak
2. **Serveren** — spelers op hun serveerplek; de serverende speler staat **buiten het veld achter de achterlijn**
3. **Rally** — plek tijdens een lopende rally
4. **Service-ontvangst** — opstelling om de service te ontvangen
5. **Verdediging midden** — verdediging tegen een midaanval van de tegenstander
6. **Verdediging rechts** — verdediging tegen een aanval over de rechterkant
7. **Verdediging links** — verdediging tegen een aanval over de linkerkant

Dat zijn **6 × 7 = 42 configuraties** die geoefend moeten kunnen worden. Het is altijd duidelijk welke rotatie en welke toestand actief is.

> **Aanname:** verdedigingsposities zijn rotatie-onafhankelijk (iedere rol gaat naar zijn functionele plek). De data-opslag (zie §6) ondersteunt echter wél een aparte set per rotatie, zodat dit later flexibel is.

---

## 4. Twee modi

### A) Oefenmodus (standaard)
De gebruiker plaatst zelf de spelers en controleert het resultaat. Zie het spelverloop in §9.

### B) Opnamemodus (record mode)
Hiermee leg ik (de bouwer/coach) de **correcte posities** vast — er zijn namelijk nog geen "juiste" posities; die worden in deze modus ingevoerd.

- Een duidelijke toggle schakelt tussen **Oefenen** en **Opnemen**.
- In opnamemodus plaats je de spelers handmatig op de juiste plek voor de huidige rotatie + toestand.
- Met de knop **Opnemen** worden de huidige posities opgeslagen als de correcte posities voor díe rotatie + toestand (zie §6, persistent).
- Toon een **overzichtsraster van 6 × 7** (rotaties × toestanden) waarin in één oogopslag te zien is wat al is opgenomen (**groen**) en wat nog open staat (**grijs**). Klikken op een cel springt naar die combinatie.
- Voeg een **"Kopieer van …"**-functie toe: begin een nieuwe toestand vanuit een al opgenomen toestand en pas de posities aan. Handig als twee toestanden qua opstelling dicht bij elkaar liggen.
- Posities moeten met kleine sleepbewegingen fijn af te stellen zijn (een tikje naar links/rechts/boven/onder).

---

## 5. Knoppen en bediening

- **Rotatieknoppen** `1 6 5 4 3 2` — kies de actieve rotatie.
- **Toestandskiezer** — kies een van de 7 toestanden (tabs of knoppen).
- **Toon** (oog-icoon 👁) — **toggle**. Toont de correcte posities als **semitransparante overlaylaag** bovenop het veld, terwijl de eigen geplaatste spelers blijven staan. Uitzetten → alleen de eigen spelers weer zichtbaar.
- **Positie** (richtkruis-icoon ⊕) — zet de spelers alvast **midden op hun rotatievak** (de Basis-opstelling van de actieve rotatie). Met animatie (zie §7).
- **Controleer** (Submit) — valideert de opstelling (zie §8).
- **Herstel** (Reset) — zet de spelers terug naar buiten het veld; opnieuw proberen.
- **Volgende** / **Terug** — schakelt door de rotaties/toestanden heen. **Let op: de door de gebruiker geplaatste spelers blijven staan bij het schakelen!**
- **Opnemen** (alleen in opnamemodus, zie §4B).
- **Exporteren** / **Importeren** (zie §8).

Kies passende, herkenbare iconen bij de knoppen.

---

## 6. Opslag (persistent, cruciaal)

De opgenomen correcte posities moeten **tussen sessies bewaard blijven** — bij een volgende oefensessie moeten ze er nog zijn.

- **Gebruik NIET `localStorage` of `sessionStorage`** — die werken niet in Claude-artefacts en laten het artefact crashen.
- Gebruik de artifact-eigen persistent storage API: `window.storage.get / set / delete / list` (async, JSON, persistent).
- Sla alles op als **één JSON-blob** onder één sleutel, bv. `v5_positions`, om losse storage-calls te vermijden. Structuur:

```json
{
  "rot1": {
    "basis":        [ { "rol": "S", "x": 0.0, "y": 0.0 }, ... ],
    "serveren":     [ ... ],
    "rally":        [ ... ],
    "ontvangst":    [ ... ],
    "verd_midden":  [ ... ],
    "verd_rechts":  [ ... ],
    "verd_links":   [ ... ]
  },
  "rot6": { ... },
  ...
}
```

- Sla coördinaten **relatief** op (bv. 0–1 fractie van veldbreedte/-hoogte), zodat ze schaalonafhankelijk zijn.
- Laad de data bij het opstarten. Vang nette fouten af (try-catch); een nog niet bestaande sleutel = nog niets opgenomen.
- Als een toestand nog niet is opgenomen, val terug op de Basis-posities van die rotatie (en geef aan dat de toestand nog niet is opgenomen).

---

## 7. Animatie

Wanneer spelers **door de app** worden verplaatst (bv. via de Positie-knop, of bij het laden van een opgenomen toestand in opnamemodus), gebeurt dat **geanimeerd**, niet ineens:

- **Eén speler tegelijk** schuift soepel over het veld naar zijn plek (CSS-transition, ~400 ms).
- **~150 ms pauze** tussen de spelers, zodat duidelijk te volgen is wie waarheen gaat.
- Suggestie voor volgorde: **Setter eerst** (als ankerpunt), daarna de rest.
- De **Toon-overlay** hoeft niet geanimeerd (dat is een statische laag die je aan/uit zet).

---

## 8. Validatie

Bij **Controleer**:

- Vergelijk elke gebruikerspositie met de opgenomen correcte positie voor de actieve rotatie + toestand.
- Binnen een instelbare **marge** → **groen vinkje** ✅ op de speler; daarbuiten → **rood kruisje** ❌.
- Standaardmarge ~8–10% van de veldafmeting; maak de marge eenvoudig aanpasbaar (bv. een kleine instelling/slider), zodat we 'm later kunnen tweaken.

### Extra (optioneel, toggle-baar): buurman-/passeerregel
Spelers mogen elkaar niet "passeren" volgens de volleybalregels — ook niet als ze binnen de marge van hun juiste plek staan:

- Een **achterspeler mag niet vóór** zijn corresponderende voorspeler staan (paren: rechts-voor/rechts-achter, midden-voor/midden-achter, links-voor/links-achter).
- Binnen een rij moet de **links-rechts-volgorde** kloppen (links-voor links van midden-voor links van rechts-voor; idem in de achterlijn).
- Welke rol in welk vak hoort, volgt uit de Basis-opstelling van de actieve rotatie. Bij overtreding: markeer de betrokken spelers als fout, ongeacht de marge.
- Bouw dit als een aan/uit-zetbare controle, zodat het uitgeschakeld kan worden als het te streng aanvoelt.

---

## 9. Het spelverloop (oefenmodus)

1. De app opent met een **kort uitlegscherm**. De gebruiker klikt op **Starten**.
2. Het is duidelijk welke **rotatie** (1/6/5/4/3/2) en welke **toestand** gevraagd wordt.
3. De gebruiker plaatst de spelers waar hij denkt dat ze horen.
4. Eventueel gebruikt hij **Toon**, **Herstel** of **Positie** als hulp.
5. Klaar? → **Controleer**: goede spelers krijgen ✅, foute ❌.
6. Met **Toon** kan hij alsnog de juiste posities bekijken (overlay).
7. Met **Herstel** kan hij het veld leegmaken en opnieuw proberen.
8. Met **Terug** / **Volgende** schakelt hij naar een andere rotatie/toestand en oefent die.

---

## 10. Export / Import

- **Exporteren**: toont de volledige `v5_positions`-JSON (in een tekstvak en/of kopieert naar klembord), zodat de gebruiker zijn opgenomen posities aan een teamgenoot kan doorgeven of een tweede systeem als losse export kan bewaren.
- **Importeren**: laat de gebruiker JSON plakken, valideert het, en laadt het in de storage (overschrijft de huidige set, na bevestiging).

---

## 11. Technische eisen

- **Eén React-bestand.**
- Strak, rustig, professioneel uiterlijk; het veld is de hoofdzaak, de bediening is duidelijk maar niet schreeuwerig.
- Veld bij voorkeur als **SVG** (scherpe lijnen, makkelijk schaalbaar) of nette div/CSS-opzet.
- **Geen** `localStorage` / `sessionStorage` — gebruik `window.storage` (zie §6).
- Houd state in React (`useState`/`useReducer`); persistente data via `window.storage`.
- Coördinaten relatief opslaan (schaalonafhankelijk).
- Werkt op desktop; drag-and-drop moet soepel en betrouwbaar zijn.

---

## 12. Bouwvolgorde (suggestie)

Begin met een werkend skelet: het veld met lijnen/net/vakken/scheidsrechtersstoel, de 6 sleepbare spelers, rotatie- en toestandskeuze, en de opnamemodus met storage. Zodra ik daarmee de Basis-toestanden kan vastleggen, bouwen we de oefenlus (Controleer, Toon-overlay, animatie, navigatie) eromheen. Lever in één keer een volledig werkend artefact op.

asdf