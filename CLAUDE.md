# Geopolitics Vardark

Iran Conflict Tracker — geopolitics.vardark.no

## Stack
- **Ren HTML** — enkel `index.html`, ingen byggesteg
- **Deploy:** Cloudflare Pages via eksplisitt Wrangler-deploy; repoet har ingen GitHub Actions- eller Git-integrasjonsdeploy
- Statisk side, ingen avhengigheter

## Innhold
- Historisk snapshot av ammunisjonstrackeren per 19. april 2026 — ikke lenger oppdatert
- Tab-basert layout: munitions → interceptors → metodikk
- Datakilder: OSINT, DoD-rapporter, nyhetsbyråer

## Konvensjoner
- Norsk/engelsk miks (teknisk innhold på engelsk)
- Mobil-responsivt
- Kildeangivelser for alle estimater

## Learnings
Les `LEARNINGS.md` — akkumulerte feil og lærdommer fra tidligere økter. Oppdater den etter hver kodeøkt med det du lærte.

## Før push
1. Åpne `index.html` lokalt og sjekk at arkivmeldingen og alle tabs vises
2. Test mobil viewport
