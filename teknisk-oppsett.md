# Om løsningen

## Formål

Formålet med løsningen er å forenkle prosessen rundt oppføring av utlegg i digitaliseringsetaten. Per dags dato finnes en slik løsning i HR-systemet, men denne er utdatert og er ikke den mest brukervennlige løsningen.

- **Løsningseier:** Nemnda / Tofik Sahraoui & Nabil Safadi
- **Løsningsforvalter:** DIG
- **Utviklet av:** DIG / Asjer Bereket

---

## Teknisk oppsett

### Miljøer

| Miljø | Komponent | Navn |
|---|---|---|
| DIG Lærlinger Dev | POWER APP | Utleggsapp |
| DIG Lærlinger Dev | POWER AUTOMATE | Planner flyt |

---

### Database oppsett (SharePoint List)

| Kolonnenavn | Type |
|---|---|
| NavnInnmelder | string |
| Epostadresse | string |
| Dato | date & time |
| Kategori | choice |
| Beskrivelse | string |
| Sum | int |
| UtleggStatus | choice |
| officeUserId | string |

---

## Power Apps

![alt text](<Images/Skjermbilde 2026-06-03 kl. 13.36.55.png>)

Som nevnt i formålet er utleggsappen en løsning som skal forenkle hverdagen til de ansatte i digitaliseringsetaten.

Løsningen består av 2 deler: en Power App og en Power Automate-flyt. Selve registreringen av utlegget skjer i appen. Når en bruker fører opp et utlegg, gjøres det en POST-request mot databasen som sender over dataen fra skjemaet.

### Mine utlegg

En ansatt har muligheten til å se status på sitt utlegg via **Mine utlegg**-siden. Denne siden er ikke tilgangsstyrt, men innholdet er det. Det gjøres en sjekk mot innlogget bruker, og kun utlegg som den personen selv har ført opp vises. En ansatt vil få varsling på et utlegg når det er ferdig behandlet av en admin.

### Behandle utlegg (admin)

Det finnes også en admin-side kalt **Behandle utlegg**. Denne siden er tilgangsstyrt, og appen gjør en sjekk om innlogget bruker har riktig rolle før de kan nå siden. Rollen til en admin settes i en collection i `onStart` i appen.

---

## Power Automate

![alt text](<Images/Skjermbilde 2026-06-03 kl. 13.37.11.png>)

Power Automate-flyten er utviklet for å varsle en ansatt om at utlegget deres er ferdig behandlet. Flyten lytter etter endringer i statuskolonnen til et utlegg og sender en e-post til tilhørende e-postadresse.

---

## Tilgangsstyring og deling

Appen er åpen for alle som er på `oslo.kommune`-domenet, det vil si alle som har `oslo.kommune.no` i e-postadressen sin. Den eneste formen for tilgangsstyring i løsningen er i selve appen, på admin-siden hvor man behandler utlegg.

---

## Virksomheter som har tatt løsningen i bruk

- Nemnda