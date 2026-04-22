# StrengthApp (PWA)

En enkel mobilvennlig webapp som viser dagens treningsøkt fra `strength_program.json`
og lar deg logge faktiske reps og vekt per sett. Eksport tilbake til PC skjer via
Google Drive (eller hvilken som helst fildeling).

## Innhold
- `index.html` – selve appen (HTML + CSS + JS i én fil)
- `manifest.webmanifest` – gjør appen installerbar som PWA
- `sw.js` – service worker (offline-støtte)
- `icon.svg` – ikon brukt på startskjermen

## Kom i gang lokalt (test på PC)
Service workers og PWA-funksjoner krever HTTP(S). Du kan kjøre en lokal server raskt
med f.eks. Python:

```
python -m http.server 8080
```

kjørt fra `app`-mappen, og så åpne http://localhost:8080 i Chrome.

## Bruk på Android (anbefalt flyt)
1. Legg `strength_program.json` i Google Drive på PC-en (dra og slipp i nettleseren eller bruk Google Drive for desktop).
2. Åpne Google Drive-appen på telefonen, vent til filen er tilgjengelig offline om nødvendig.
3. Åpne appens URL i Chrome på telefonen (se "Publisere" under).
4. Trykk `Velg programfil`, finn `strength_program.json` via filvelgeren (Google Drive vises som kilde).
5. Appen foreslår dagens økt basert på ukedag (man=Upper A, tir=Lower A, osv.). Bruk dropdowns øverst hvis du vil bytte uke/dag.
6. Fyll inn faktiske `reps` og `vekt (kg)` per sett. Trykk ringen til høyre for å markere et sett som fullført.
7. Alt lagres automatisk lokalt på telefonen (`localStorage`).
8. Når økta er ferdig: trykk `Eksporter`. Android viser delemenyen — velg `Google Drive` (eller `Lagre i Drive`). Filnavn: `strength_program_logged_<dato>.json`.
9. På PC-en henter du den oppdaterte filen fra Google Drive og behandler dataene som før.

### Installere som "app"
I Chrome på Android: meny → `Legg til på startskjermen`. Ikonet fungerer da som en vanlig app.

## Publisere (velg én)

### Alternativ A – GitHub Pages (anbefalt)
1. Opprett et repo på GitHub og last opp innholdet i `app`-mappen til repo-roten.
2. Aktiver Pages under `Settings → Pages` (branch: `main`, mappe: `/`).
3. Etter et par minutter får du en URL (f.eks. `https://brukernavn.github.io/reponavn/`). Åpne den i Chrome på telefonen.

### Alternativ B – Kjør fra lokal fil
Legg hele `app`-mappen i Google Drive. Åpne `index.html` i Chrome via Drive / Filer-appen.
PWA-funksjoner (installasjon, offline) fungerer best med en ordentlig HTTPS-URL, så
alternativ A anbefales.

## Datamodell
Loggede verdier legges til i hver `exercise` som `actual_sets`:

```json
{
  "name": "Back squat",
  "sets": 5,
  "reps": 4,
  "load_kg": 85.0,
  "actual_sets": [
    { "reps": 4, "load_kg": 85, "completed": true, "completed_at": "2026-04-20T11:15:00.000Z" }
  ]
}
```

Resten av originalstrukturen er urørt. Eksportert fil er altså samme program som
ble lastet inn, men med `actual_sets` fylt inn på de øvelsene du har logget.

### Sammenkoblede sett (heavy/back-off)
For øvelser der `load_kg` er et objekt med `heavy`/`backoff` (f.eks. mandagens
Barbell bench press: `2 reps × 90 kg → 15 reps × 60 kg`) blir hvert sett
registrert som to sammenhengende rader (Tung + Back-off), og `actual_sets`
får formen:

```json
{
  "actual_sets": [
    {
      "heavy":   { "reps": 2,  "load_kg": 90, "completed": true, "completed_at": "..." },
      "backoff": { "reps": 15, "load_kg": 60, "completed": true, "completed_at": "..." }
    }
  ]
}
```

## Begrensninger i MVP
- Logger bare `reps` og `vekt` per sett (ikke RPE eller kommentar per sett).
- Ingen hvile-timer.
- Ingen direkte Google Drive-API-integrasjon (går via Android-delemenyen eller nedlasting).

## Tilbakestille
Fra Chrome-menyen: `Innstillinger → Personvern → Slett data` for siden, eller bruk
DevTools (`Application → Storage → Clear site data`) på PC.
