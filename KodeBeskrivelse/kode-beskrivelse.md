# Kodebase i Power Apps

> Ettersom at jeg har valgt å løse min oppgave med Power Platform (Power Apps, Power Automate) er det ikke mulig å laste opp kodebasen til en versjonskontroll som f.eks Github. Derfor har jeg denne filen hvor jeg laster opp code-snippets av viktig kode som er med på å få appen til å funke, og gir en kort beskrivelse av koden.

---

## Konfig fil

En viktig del av appen er konfig-filen som lar appen benytte seg av design-biblioteket til Oslo kommune for Power Apps. I config-filen finner man blant annet fargene, font og font-størrelse som Oslo kommune bruker.

```javascript
// ─── Farger ───────────────────────────────────────────────────────────────────

varColorBlueState          = ColorValue("#1f42aa");
varColorDarkBlue           = ColorValue("#2A2859");
varColorBlue               = ColorValue("#6FE9FF");
varColorDarkGreen          = ColorValue("#034B45");
varColorGreen              = ColorValue("#43F8B6");
varColorLightGreen         = ColorValue("#C7F6C9");
varColorRed                = ColorValue("#FF8274");
varColorYellow             = ColorValue("#F9C66B");
varColorBeige              = ColorValue("#D0BFAE");
varColorGray               = ColorValue("#CCCCCC");
varColorWhite              = ColorValue("#ffffff");
varColorPurple             = ColorValue("#e0adff");

// ─── Standard farger ──────────────────────────────────────────────────────────

varColorDefaultLightBlue   = ColorValue("#D1F9FF");
varColorDefaultLightGreen  = ColorValue("#C7FDE9");
varColorDefaultFadedGreen  = ColorValue("#E5FFE6");
varColorDefaultFadedRed    = ColorValue("#FFDFDC");
varColorDefaultRed         = ColorValue("#FFB4AC");
varColorDefaultLightBeige  = ColorValue("#F8F0DD");
varColorDefaultYellow      = ColorValue("#FFE7BC");
varColorDefaultGray        = ColorValue("#F9F9F9");
varColorBlue100            = ColorValue("#F1FDFF");

// ─── Gråskala ─────────────────────────────────────────────────────────────────

varColorGray900            = ColorValue("#1A1A1A");
varColorGray800            = ColorValue("#333333");
varColorGray700            = ColorValue("#4D4D4D");
varColorGray600            = ColorValue("#666666");
varColorGray500            = ColorValue("#808080");
varColorGray400            = ColorValue("#9A9A9A");
varColorGray300            = ColorValue("#B3B3B3");
varColorGray200            = ColorValue("#CCCCCC");
varColorGray100            = ColorValue("#E6E6E6");

// ─── Bakgrunnsfarger ──────────────────────────────────────────────────────────

varColorBackgroundDefault  = ColorValue("#FFFFFF");
varColorBackgroundSubtle   = ColorValue("#F9F9F9");
varColorBackgroundSubtle0  = ColorValue("#2C2C2C");
varColorBackgroundCard     = ColorValue("#FFFFFF");

// ─── Font ─────────────────────────────────────────────────────────────────────

varOsloFont = { Standardfont: "Oslo Sans Office, Arial" };

// ─── Font-størrelser (NATO-skala) ─────────────────────────────────────────────

Alpha    = 54;   // Største overskrift
Bravo    = 40;
Charlie  = 36;
Delta    = 30;
Echo     = 28;
Foxtrot  = 26;
Hotel    = 22;
Juliett  = 18;
Kilo     = 16;
Lima     = 14;   // Minste tekst

// ─── Office 365-bruker ────────────────────────────────────────────────────────

varUser      = Office365Users.MyProfileV2().displayName;
varUserMail  = Office365Users.MyProfileV2().mail;
varUserId    = Office365Users.MyProfileV2().id;
```

## Registrering av utlegg

Registrering er en av hovedfunksjonene i appen. Koden er ganske enkel ettersom vi bruker Power Apps sin innebygde form-komponent til å skrive til databasen.

```javascript
SubmitForm(Form2);
ResetForm(Form2);
Set(showSuccessModal, true);
```

| Funksjon | Beskrivelse |
|---|---|
| `SubmitForm()` | Tar inn input-verdier fra Power Apps form-komponenten og skriver til databasen. Databasekoblingen defineres i selve formen, hvor man velger hvilken datakilde formen skal skrive til. |
| `ResetForm()` | Resetter alle inputfelt i formen. |
| `Set()` | Viser bekreftelsesmodal når formen er sendt inn. |

## Mine utlegg

«Mine utlegg»-siden gir en ansatt oversikt over sine egne utlegg. En kritisk del av siden er å sikre at man kun ser sine egne utlegg — ikke andre ansattes. Dette løses med følgende kodesnutt:

```javascript
Sort(
    Filter(FagprøveUtlegg, Epostadresse = varUserMail),
    Created,
    SortOrder.Descending
)
```

| Del | Beskrivelse |
|---|---|
| `Filter()` | Filtrerer databasen `FagprøveUtlegg` og returnerer kun rader der `Epostadresse`-kolonnen matcher den innloggede brukerens e-post (`varUserMail`). |
| `Sort()` | Sorterer de filtrerte resultatene på `Created`-feltet i synkende rekkefølge — nyeste utlegg vises øverst.

## Behandle utlegg

«Behandle utlegg»-siden ligner på «Mine utlegg», men er kun tilgjengelig for admins. Her får admins en oversikt over alle registrerte utlegg og kan behandle dem.

For å sikre at kun admins har tilgang, brukes følgende tilgangssjekk:

```javascript
CountIf(colAdminList, Value = Lower(varUserMail)) > 0
```

| Del | Beskrivelse |
|---|---|
| `colAdminList` | En collection i kodebasen som inneholder e-postadressene til alle admins. |
| `Lower(varUserMail)` | Konverterer den innloggede brukerens e-post til lowercase for å sikre case-insensitiv sammenligning. |
| `CountIf(...) > 0` | Returnerer `true` hvis brukeren finnes i adminlisten — ellers får de opp en «Du har ikke tilgang»-side.

---

## Avslutning

Dette er hovedkoden og funksjonaliteten som driver utleggsappen. For mer informasjon om den tekniske infrastrukturen, se teknisk oppsett:

> [Teknisk Oppsett](https://github.com/asjerab/AsjerFagprove/blob/main/teknisk-oppsett.md)