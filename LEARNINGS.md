# Learnings — geopolitics-vardark

Automatisk oppdatert etter kodeøkter. Leses ved oppstart av ny økt.

## 2026-07-17
- **Arkivering fremfor sletting:** Når trackeren avsluttes, behold historiske data og legg en tydelig, datert arkivmelding øverst; ikke la en gammel "Active Conflict"-status bli stående.
- **Tredjepartsdiagrammer:** CDN-skript bruker SRI-hash og `crossorigin="anonymous"`. `_headers` begrenser eksterne scripts til cdnjs og setter basale sikkerhetsheaders, men inline legacy-kode betyr at CSP fortsatt må tillate `unsafe-inline`.
- **Deploy-kontrakt:** Git-push publiserer ikke geopolitics automatisk. Deploy en stagingmappe med både `index.html` og `_headers` via Wrangler, ellers blir enten endringen eller sikkerhetsheaderne stående lokalt.

## 2026-03-25
- Ren HTML, ingen byggesteg — åpne `index.html` direkte for testing.
- Deployet via Cloudflare Pages (bekreftet live på geopolitics.vardark.no).
