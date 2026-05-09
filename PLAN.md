# Plan razvoja — Moj namaz by Divan

**Cilj:** Dovesti app sa MVP+ na profi nivo prije launcha.
**Polazna verzija:** v2.26.25 (9. maj 2026.)
**Backup tag:** `pre-sprint-1`

## Legenda
- `[ ]` — nije urađeno
- `[⏳]` — u toku
- `[x]` — gotovo (uz verziju)
- `[O]` — Opus radi
- `[S]` — Sonnet radi
- `[-]` — korisnik (Ajdin) radi

## Sigurnosna pravila izvršavanja
1. Atomic commits — jedan commit po tački
2. Bez razdvajanja fajla u prva 2 sprinta
3. Test poslije svakog commit-a
4. Backup tag prije svakog sprinta (`git tag pre-sprint-N`)
5. Logo, ikone, branding — Ajdin radi i zamjenjuje na kraju

---

## SPRINT 1 — TEMELJI (sigurnost + PWA + funkcionalnost)

### Sigurnost
- [ ] [O] 1.1 Kreirati `escapeHTML(s)` helper
- [ ] [S] 1.2 Sanitizirati `gradBA` (linija 4317)
- [ ] [S] 1.3 Sanitizirati Nominatim odgovor (`grad`, `drzava` u liniji 4505)
- [ ] [S] 1.4 Sanitizirati URL `?city=` parametar (linija 3116)
- [ ] [O] 1.5 CSP meta tag
- [ ] [S] 1.6 Validirati `vakat[]` array format (svaki `HH:MM`)
- [ ] [S] 1.7 Sigurniji parsing `?time=` URL parametra

### PWA
- [ ] [S] 2.1 Update `manifest.json` — "Moj namaz by Divan"
- [ ] [S] 2.2 Dodati `lang:'bs'`, `categories`, `screenshots` u manifest
- [ ] [O] 2.3 Kreirati `service-worker.js` (cache-first stat, network-first API)
- [ ] [S] 2.4 Registrovati SW u `index.html`
- [ ] [S] 2.5 `<link rel="apple-touch-icon" href="icon-192.png">`
- [ ] [S] 2.6 `<meta name="format-detection" content="telephone=no">`
- [ ] [S] 2.7 `<meta name="color-scheme" content="dark">`
- [ ] [S] 2.8 `<noscript>` poruka
- [ ] [-] 2.9 iOS splash screen (Ajdin pravi PNG-ove, ja dodam meta)
- [ ] [S] 2.10 `theme-color` light/dark variante

### Funkcionalnost
- [ ] [O] 3.0b Polarni edge case fallback (Skandinavija ljeti)
- [ ] [O] 3.1 Refaktorisanje 11 monkey-patched funkcija
- [ ] [O] 3.2 Konsolidacija 5 setInterval timera u jedan master tick
- [ ] [O] 3.3 Sehur countdown koristi zoraBA za BiH (linija 5805)
- [ ] [S] 3.4 Pull-to-refresh na svim panelima
- [ ] [S] 3.5 GPS `enableHighAccuracy:true` na prvom loadu
- [ ] [O] 3.6 Globalni error boundary (`window.onerror`, `unhandledrejection`)
- [ ] [S] 3.7 Provjera VAKTIJA_GRADOVI (Mitrovica, Preševo, Bujanovac)
- [ ] [S] 3.8 Brisanje dead code (`ni_last_location`, duplikat `fajrUK`/`sehurKraj`)

---

## SPRINT 2 — DIZAJN POLISH

### Vizuelna konzistentnost
- [ ] [-] 4.1 Provjera logo SVG vizualno (Ajdin gleda)
- [ ] [O] 4.2 Tokenizirati rgba boje u CSS varijable
- [ ] [S] 4.3 Konsolidovati border-radius (sve na `--r-*` tokene)
- [ ] [O] 4.4 Konsolidovati `font-size` sistem
- [ ] [O] 4.5 Ukloniti ~50 `!important` deklaracija
- [ ] [O] 4.6 Konsolidovati duplicate `@media` blokove
- [ ] [O] 4.7 Ujednačiti emoji vs SVG ikone
- [ ] [S] 4.8 Konzistentne warn poruke

### Responsive & Desktop
- [ ] [O] 5.1 Desktop wrapper `max-width:480px` iznad 768px
- [ ] [O] 5.2 Desktop background art
- [ ] [S] 5.3 iPhone SE 320px sječenje test
- [ ] [O] 5.4 iPad portrait layout
- [ ] [S] 5.5 Landscape orientation handling
- [ ] [S] 5.6 Touch target 44×44 (info dugme)
- [ ] [S] 5.7 Wake lock dugme touch target

### Tipografija & Spacing
- [ ] [O] 6.1 Letter-spacing skala (4 tokena)
- [ ] [O] 6.2 Line-height skala (4-5 tokena)
- [ ] [O] 6.3 Spacing tokeni (`--s-1` do `--s-8`, 4/8 grid)
- [ ] [S] 6.4 iOS Dynamic Type test
- [ ] [S] 6.5 Font preload

### Komponente & Stanja
- [ ] [S] 7.1 `:focus-visible` stilovi za sve dugmiće
- [ ] [S] 7.2 `:hover` desktop stanja
- [ ] [S] 7.3 `:disabled` state stilovi
- [ ] [O] 7.4 Loading skeleton — countdown
- [ ] [O] 7.5 Loading skeleton — vakat lista
- [ ] [O] 7.6 Empty state ekrani
- [ ] [O] 7.7 Error toast/snackbar

### Micro-interactions
- [ ] [O] 8.1 Tab transition animacija
- [ ] [S] 8.2 Aktivacija ripple efekta
- [ ] [S] 8.3 Backdrop blur na modalima
- [ ] [S] 8.4 Pulsacija live timera (zadnja sekunda)
- [ ] [O] 8.5 Smooth update kalendara u podne
- [ ] [S] 8.6 Haptic feedback

### Branding (Ajdin radi assets, ja dodajem)
- [ ] [-] 9.1 Novi logo (Ajdin dizajnira)
- [ ] [-] 9.2 App icon set (Ajdin dizajnira)
- [ ] [S] 9.3 Splash screen za iOS PWA (kad Ajdin da PNG)
- [ ] [S] 9.4 OG image za social sharing
- [ ] [S] 9.5 Favicon set

---

## SPRINT 3 — RUČNI UNOS LOKACIJE

- [ ] [O] 13.1a Database lokacija (~280 gradova, JSON)
- [ ] [O] 13.1b UI modal "Odaberi lokaciju"
- [ ] [O] 13.1c Logika fallback (URL → manual → GPS → fallback ekran)
- [ ] [O] 13.1d Persistencija u localStorage
- [ ] [O] 13.1e Indikator "ručno postavljeno" + reset

### Tekstovi (paralelno)
- [ ] [S] 10.1 Proširiti info modal
- [ ] [S] 10.2 Konzistentnost velika/mala slova
- [ ] [S] 10.3 Crtice u "kraj sabah-namaza" / "kraj duha-namaza"
- [ ] [S] 10.4 Proširiti `formatBosnianCountry` ili `Intl.DisplayNames`
- [ ] [O] 10.5 Predstojeći događaji opisi
- [ ] [S] 10.6 Pregledati warn tekstove

---

## SPRINT 4 — ARHITEKTURA (razdvajanje fajla)

⚠️ **Najveći rizik — radi se zadnje, kad sve gore radi i testirano je.**

- [ ] [O] 11.1 Razdvajanje CSS u module (`tokens`, `base`, `components`, `danas`, `kibla`, `predstojece`, `responsive`)
- [ ] [O] 11.2 Razdvajanje JS u ES6 module (`config`, `time`, `regions`, `api`, `countdown`, `ui-*`, `app`)
- [ ] [S] 11.4 VAKTIJA_GRADOVI → vanjski JSON
- [ ] [S] 11.5 Logo SVG → vanjski fajl (kad Ajdin da finalan)
- [ ] [S] 11.6 `console.log` cleanup za produkciju

### Performance
- [ ] [S] 12.2 Pause timeri kad `document.hidden`
- [ ] [O] 12.3 Lazy-init kibla i predstojece tabovi
- [ ] [S] 12.4 Preload critical fonts
- [ ] [S] 12.5 Optimizacija ikona

---

## SPRINT 5 — VERIFIKACIJA

- [ ] [S] 14.1 Lighthouse audit (cilj 95+ na sve)
- [ ] [S] 14.2 Test 13 countdown faza (Tuzla, Mekka, Berlin, Istanbul, Stockholm)
- [ ] [S] 14.3 iPhone Safari + PWA install
- [ ] [S] 14.4 Android Chrome + PWA install
- [ ] [S] 14.5 Desktop Chrome/Safari/Firefox/Edge
- [ ] [S] 14.6 Offline mode test
- [ ] [S] 14.7 API failure test (vaktija blokirana)
- [ ] [S] 14.8 iOS Accessibility test
- [ ] [O] 14.9 XSS penetration test
- [ ] [-] 14.10 Šejh review (Ajdin pokreće)

---

## Istorija izvršavanja

| Datum | Verzija | Tačka | Šta urađeno | Status |
|-------|---------|-------|-------------|--------|
| 2026-05-09 | v2.26.25 | -- | Polazno stanje + PLAN.md | ✓ |
